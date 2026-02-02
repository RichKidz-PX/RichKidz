repeat task.wait() until game:IsLoaded()
local Player = game:GetService("Players")
local ReplicatedStorage = game:GetService("ReplicatedStorage")
local CollectionService = game:GetService("CollectionService")
local GuiService = game:GetService("GuiService")
local Stime = tick()
local Respawn = true
local ErrorChest = {}
local HttpService = game:GetService("HttpService")
local TeleportService = game:GetService("TeleportService")
local JobId = game.JobId
local PlaceId=game.PlaceId
task.spawn(function()
local Team = ReplicatedStorage.Remotes.CommF_:InvokeServer("SetTeam","Marines")
end)
local url = "https://games.roblox.com/v1/games/" .. PlaceId .. "/servers/Public?sortOrder=Asc&limit=100&t="
local function HopServer()
    local success,result
    repeat
    success,result = pcall(function()
    return HttpService:JSONDecode(game:HttpGet(url))
    end)
    task.wait(1)
    until success and result and result.data
    local Server= result.data
    local MainServer = Player.LocalPlayer.UserId % 100 + 1
    local StartServer = math.max(1 , MainServer - 2)
    local LastServer = math.min(100,MainServer + 2)
    for i = StartServer , LastServer do 
        if Server[i].id ~= JobId and Server[i].playing < Server[i].maxPlayers then
            pcall(function()
			TeleportService:TeleportToPlaceInstance(PlaceId , Server[i].id)
			end)
	    task.wait(2)
        end
    end
end
local function autochest()
    local foundchest = false
    local Character=Player.LocalPlayer.Character
    if not Character or not Character:FindFirstChild("HumanoidRootPart") or not Character:FindFirstChild("Humanoid") then
        return 
    end
    local RootPart=Character:FindFirstChild("HumanoidRootPart")
    if Character:FindFirstChild("Humanoid").Health <= 0 or not RootPart then
        Respawn = true
        return
    end
    if Respawn and Character:FindFirstChild("Humanoid").Health > 0 and RootPart then
        Stime = tick()
    end
for i,v in ipairs(CollectionService:GetTagged("_ChestTagged")) do
    local StartTime = tick()
    if ErrorChest[v] then continue end
    if tick() - Stime >= 10 then
        Character:FindFirstChild("Humanoid").Health = 0 
        break
    end
    if v:IsA("BasePart") and not v:GetAttribute("IsDisabled") and RootPart.Parent then
        foundchest = true
        while not v:GetAttribute("IsDisabled") do 
            RootPart.CFrame = v.CFrame * CFrame.new(0,2,0)
            task.wait(0.01)
            RootPart.CFrame = v.CFrame * CFrame.new(0,1,0)
            task.wait(0.01)
            if tick() - StartTime >=5 then
                ErrorChest[v] = "True"
                break
            end
        end
    end
end
    Respawn = false
    if not foundchest then
        HopServer()
    end
end
task.spawn(function()
while true do
    autochest()
    task.wait(0.5)
end
end)
GuiService.ErrorMessageChanged:Connect(function() 
    HopServer() 
end)

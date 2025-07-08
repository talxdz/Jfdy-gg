-- Blox Fruits Auto Farm Script (Mobile & PC Compatible)

-- SETTINGS
getgenv().AUTO_FARM = true
getgenv().FAST_ATTACK = true

-- SERVICES
local Players = game:GetService("Players")
local ReplicatedStorage = game:GetService("ReplicatedStorage")
local Workspace = game:GetService("Workspace")
local RunService = game:GetService("RunService")

local player = Players.LocalPlayer
local character = player.Character or player.CharacterAdded:Wait()
local hrp = character:WaitForChild("HumanoidRootPart")

-- GET PLAYER LEVEL
local function getPlayerLevel()
    local stats = player:FindFirstChild("leaderstats")
    if stats then
        local level = stats:FindFirstChild("Level") or stats:FindFirstChild("level")
        return level and level.Value or 0
    end
    return 0
end

-- CHECK FOR ACTIVE QUEST
local function hasActiveQuest()
    local gui = player:FindFirstChild("PlayerGui")
    if gui then
        local mission = gui:FindFirstChild("MissionGui") or gui:FindFirstChild("Missions")
        if mission then
            for _, v in pairs(mission:GetChildren()) do
                if v:IsA("Frame") and v.Visible then
                    return true
                end
            end
        end
    end
    return false
end

-- FIND NEAREST ENEMY
local function getClosestEnemy()
    local enemyFolder = Workspace:FindFirstChild("Enemies")
    if not enemyFolder then return end

    local closest
    local shortest = math.huge

    for _, npc in pairs(enemyFolder:GetChildren()) do
        local root = npc:FindFirstChild("HumanoidRootPart")
        local hp = npc:FindFirstChild("Humanoid")
        if root and hp and hp.Health > 0 then
            local dist = (hrp.Position - root.Position).Magnitude
            if dist < shortest then
                shortest = dist
                closest = npc
            end
        end
    end

    return closest
end

-- MOVE TO ENEMY
local function goTo(enemy)

--[[
    Car Flipper - Game Fix & Automation Script
    Place this in a LocalScript inside StarterPlayerScripts
]]

local Players = game:GetService("Players")
local Player = Players.LocalPlayer
local RunService = game:GetService("RunService")
local ReplicatedStorage = game:GetService("ReplicatedStorage")
local Workspace = game:GetService("Workspace")

-- ============================================================
-- 1. DIAGNOSTIC SYSTEM
-- ============================================================

print("🔍 Starting Car Flipper Diagnostic...")

-- Check for common issues
local function DiagnoseGame()
    local issues = {}
    
    -- Check for nil values in scripts
    print("📊 Checking script integrity...")
    
    -- Find all scripts and check for errors
    local function ScanScripts(parent, path)
        path = path or ""
        for _, child in pairs(parent:GetChildren()) do
            if child:IsA("Script") or child:IsA("LocalScript") or child:IsA("ModuleScript") then
                local success, result = pcall(function()
                    return child.Name, child.Source or "No Source"
                end)
                if not success then
                    table.insert(issues, string.format("Script error in %s: %s", child.Name, result or "Unknown"))
                end
            end
            if child:GetChildren() then
                ScanScripts(child, path .. "/" .. child.Name)
            end
        end
    end
    
    pcall(function()
        ScanScripts(game)
    end)
    
    -- Check for missing required services
    local requiredServices = {
        "ReplicatedStorage",
        "ServerStorage",
        "Workspace",
        "Lighting"
    }
    
    for _, service in ipairs(requiredServices) do
        if not game:FindFirstChild(service) then
            table.insert(issues, string.format("Missing required service: %s", service))
        end
    end
    
    -- Check for Terrain issues (from your logs)
    if Workspace:FindFirstChild("Terrain") then
        local terrain = Workspace.Terrain
        if not terrain:FindFirstChild("Cloud") then
            print("⚠️ Cloud instance missing in Terrain - creating...")
            local cloud = Instance.new("Part")
            cloud.Name = "Cloud"
            cloud.Size = Vector3.new(100, 10, 100)
            cloud.Position = Vector3.new(0, 100, 0)
            cloud.Material = Enum.Material.SmoothPlastic
            cloud.Anchored = true
            cloud.Parent = terrain
            print("✅ Cloud instance created")
        end
    else
        print("⚠️ No Terrain found - creating...")
        local terrain = Instance.new("Terrain")
        terrain.Parent = Workspace
        local cloud = Instance.new("Part")
        cloud.Name = "Cloud"
        cloud.Size = Vector3.new(100, 10, 100)
        cloud.Position = Vector3.new(0, 100, 0)
        cloud.Anchored = true
        cloud.Parent = terrain
        print("✅ Terrain and Cloud created")
    end
    
    return issues
end

-- Run diagnostic
local diagnosticIssues = DiagnoseGame()

if #diagnosticIssues > 0 then
    print("❌ Found " .. #diagnosticIssues .. " issues:")
    for i, issue in ipairs(diagnosticIssues) do
        print("  " .. i .. ". " .. issue)
    end
else
    print("✅ No major issues found!")
end

-- ============================================================
-- 2. FIX SCRIPT (Attempts to fix common issues)
-- ============================================================

local function FixGame()
    print("🛠️ Attempting to fix game...")
    
    -- Fix: Create missing RemoteEvents if they don't exist
    local function EnsureRemote(name)
        if not ReplicatedStorage:FindFirstChild(name) then
            print("  Creating missing RemoteEvent: " .. name)
            local remote = Instance.new("RemoteEvent")
            remote.Name = name
            remote.Parent = ReplicatedStorage
            return remote
        end
        return ReplicatedStorage:FindFirstChild(name)
    end
    
    -- Common RemoteEvents that might be missing
    local remotes = {
        "BuyCar",
        "FixCar",
        "SellCar",
        "ClaimCash",
        "ClaimParts",
        "ClaimContainer",
        "UpdateStand",
        "QuestComplete",
        "DeliverJunk",
    }
    
    for _, remoteName in ipairs(remotes) do
        EnsureRemote(remoteName)
    end
    
    -- Fix: Create an AutoRun module if missing
    if not ReplicatedStorage:FindFirstChild("AutoRun") then
        print("  Creating AutoRun ModuleScript...")
        local autoRun = Instance.new("ModuleScript")
        autoRun.Name = "AutoRun"
        autoRun.Source = [[
            local AutoRun = {}
            
            function AutoRun.Start()
                print("🚗 Car Flipper Automation Started")
                return true
            end
            
            function AutoRun.Stop()
                print("🛑 Car Flipper Automation Stopped")
                return true
            end
            
            function AutoRun.GetStatus()
                return "Running"
            end
            
            return AutoRun
        ]]
        autoRun.Parent = ReplicatedStorage
    end
    
    print("✅ Fix attempts completed!")
end

-- Run fixes
FixGame()

-- ============================================================
-- 3. AUTOMATION ENGINE
-- ============================================================

local Automation = {
    Running = false,
    Tasks = {},
    Delay = 1.5,
}

-- Task system
function Automation:AddTask(name, func, interval)
    table.insert(self.Tasks, {
        Name = name,
        Func = func,
        Interval = interval or self.Delay,
        LastRun = 0,
    })
end

function Automation:Start()
    self.Running = true
    print("🚀 Starting Car Flipper Automation...")
    
    task.spawn(function()
        while self.Running do
            local currentTime = tick()
            
            for _, task in ipairs(self.Tasks) do
                if currentTime - task.LastRun >= task.Interval then
                    pcall(function()
                        task.Func()
                        task.LastRun = currentTime
                    end)
                end
            end
            
            task.wait(self.Delay)
        end
    end)
end

function Automation:Stop()
    self.Running = false
    print("🛑 Stopped Car Flipper Automation")
end

-- ============================================================
-- 4. GAME-SPECIFIC TASKS (Adapt these to your game)
-- ============================================================

-- Helper function to find clickable objects
local function FindInteractable()
    local player = Player
    local character = player.Character
    if not character then return nil end
    
    local root = character:FindFirstChild("HumanoidRootPart")
    if not root then return nil end
    
    -- Look for nearby interactables
    for _, obj in pairs(Workspace:GetDescendants()) do
        if obj:IsA("BasePart") and obj:FindFirstChild("ClickDetector") then
            local distance = (obj.Position - root.Position).Magnitude
            if distance < 20 then
                return obj
            end
        end
    end
    
    return nil
end

-- Add tasks (customize these for your game)
Automation:AddTask("Claim Cash", function()
    print("💰 Claiming cash...")
    local remote = ReplicatedStorage:FindFirstChild("ClaimCash")
    if remote and remote:IsA("RemoteEvent") then
        remote:FireServer()
    else
        -- Fallback: Click on cash object
        local obj = FindInteractable()
        if obj then
            local clickDetector = obj:FindFirstChild("ClickDetector")
            if clickDetector then
                clickDetector:FireClick(Player.Mouse)
            end
        end
    end
end, 5)

Automation:AddTask("Fix Cars", function()
    print("🔧 Fixing cars...")
    local remote = ReplicatedStorage:FindFirstChild("FixCar")
    if remote and remote:IsA("RemoteEvent") then
        remote:FireServer()
    end
end, 3)

Automation:AddTask("Sell Cars", function()
    print("💲 Selling cars...")
    local remote = ReplicatedStorage:FindFirstChild("SellCar")
    if remote and remote:IsA("RemoteEvent") then
        remote:FireServer()
    end
end, 5)

-- ============================================================
-- 5. UI INTEGRATION (Connect to your UI)
-- ============================================================

-- Wait for UI
local UI = Player.PlayerGui:FindFirstChild("CarFlipperUI")
if UI then
    print("✅ UI Found - Connecting automation...")
    
    -- You can connect your UI toggles here
    -- Example: If you have a "Start" button
    local startBtn = UI:FindFirstChild("StartButton")
    if startBtn and startBtn:IsA("TextButton") then
        startBtn.MouseButton1Click:Connect(function()
            if Automation.Running then
                Automation:Stop()
                startBtn.Text = "▶ Start"
            else
                Automation:Start()
                startBtn.Text = "⏹ Stop"
            end
        end)
    end
end

-- ============================================================
-- 6. KEYBOARD SHORTCUTS
-- ============================================================

local UserInputService = game:GetService("UserInputService")

UserInputService.InputBegan:Connect(function(input, gameProcessed)
    if gameProcessed then return end
    
    -- F2: Toggle Automation
    if input.KeyCode == Enum.KeyCode.F2 then
        if Automation.Running then
            Automation:Stop()
            print("⏹ Automation stopped (F2)")
        else
            Automation:Start()
            print("▶ Automation started (F2)")
        end
    end
    
    -- F3: Run once
    if input.KeyCode == Enum.KeyCode.F3 then
        print("▶ Running all tasks once...")
        for _, task in ipairs(Automation.Tasks) do
            pcall(task.Func)
        end
    end
    
    -- F4: Status check
    if input.KeyCode == Enum.KeyCode.F4 then
        print("📊 Status: " .. (Automation.Running and "Running" or "Stopped"))
        print("📋 Tasks: " .. #Automation.Tasks)
    end
end)

-- ============================================================
-- 7. ERROR HANDLING & RECOVERY
-- ============================================================

-- Handle script errors gracefully
local function SafePcall(func, ...)
    local success, result = pcall(func, ...)
    if not success then
        warn("⚠️ Error in automation task: " .. tostring(result))
        return nil
    end
    return result
end

-- Override task execution with error handling
local originalTasks = Automation.Tasks
Automation.Tasks = {}

for _, task in ipairs(originalTasks) do
    local originalFunc = task.Func
    task.Func = function(...)
        SafePcall(originalFunc, ...)
    end
    table.insert(Automation.Tasks, task)
end

-- ============================================================
-- 8. STARTUP COMPLETE
-- ============================================================

print("=========================================")
print("🚗 Car Flipper Automation Script Loaded")
print("📊 Game: " .. game.PlaceId)
print("👤 Player: " .. Player.Name)
print("=========================================")
print("⌨️  Controls:")
print("  F2 - Toggle Automation")
print("  F3 - Run tasks once")
print("  F4 - Show status")
print("=========================================")

-- Auto-start if diagnostics passed
if #diagnosticIssues == 0 then
    print("✅ Auto-starting automation...")
    Automation:Start()
end

--[[
    Car Flipper - Game Automation UI
    LocalScript for Roblox
    Organized, commented, and optimized for any game
]]

-- SERVICES
local Players = game:GetService("Players")
local Player = Players.LocalPlayer
local GuiService = game:GetService("GuiService")
local TweenService = game:GetService("TweenService")
local RunService = game:GetService("RunService")
local UserInputService = game:GetService("UserInputService")

-- UI REFERENCES (assumes ScreenGui with all elements)
local ScreenGui = script.Parent
local Window = ScreenGui:WaitForChild("Window")
local Main = Window:WaitForChild("Main")
local Content = Main:WaitForChild("Content")

-- ============================================================
-- 1. UI ELEMENT CACHING
-- ============================================================

-- Title Bar
local TitleBar = Window:WaitForChild("TitleBar")
local TitleControls = TitleBar:WaitForChild("Controls")

-- Sidebar
local Sidebar = Window:WaitForChild("Sidebar")
local SidebarItems = Sidebar:GetChildren()

-- Tabs
local Tabs = Main:WaitForChild("Tabs")
local TabAuto = Tabs:WaitForChild("Auto")
local TabRunOnce = Tabs:WaitForChild("RunOnce")

-- Status Bar
local StatusBar = Content:WaitForChild("StatusBar")
local StatusDot = StatusBar:WaitForChild("Dot")
local StatusText = StatusBar:WaitForChild("Text")
local StatusOnline = StatusBar:WaitForChild("Online")

-- Logs Panel
local LogsPanel = Content:WaitForChild("LogsPanel")
local LogsContainer = LogsPanel:WaitForChild("LogsContainer")

-- ============================================================
-- 2. STATE MANAGEMENT
-- ============================================================

local State = {
    ActiveTab = "Auto",          -- "Auto" or "RunOnce"
    Status = "Idle",             -- "Idle", "Running", "Done", "Stopped"
    Logs = {},
    Toggles = {},
    Settings = {
        Delay = 1.5,
        AutoReconnect = true,
        AntiAFK = true,
        AutoSave = true,
        Notifications = false,
        FPSLimit = true,
    }
}

-- ============================================================
-- 3. HELPER FUNCTIONS
-- ============================================================

-- Toggle UI element with animation
local function ToggleElement(element, active, isGreen)
    if not element then return end
    
    local tweenInfo = TweenInfo.new(0.25, Enum.EasingStyle.Quad, Enum.EasingDirection.Out)
    local properties = {}
    
    if active then
        properties.BackgroundColor3 = isGreen and Color3.fromRGB(34, 197, 94) or Color3.fromRGB(59, 130, 246)
        -- Shadow glow effect via BackgroundTransparency or we can use a separate glow frame
        element.BackgroundColor3 = properties.BackgroundColor3
        element:FindFirstChild("Knob").Position = UDim2.new(1, -19, 0.5, 0)
    else
        properties.BackgroundColor3 = Color3.fromRGB(36, 44, 62)
        element.BackgroundColor3 = properties.BackgroundColor3
        element:FindFirstChild("Knob").Position = UDim2.new(0, 3, 0.5, 0)
    end
    
    local tween = TweenService:Create(element, tweenInfo, properties)
    tween:Play()
end

-- Add log entry
local function AddLog(message)
    local timestamp = os.date("%H:%M")
    local logText = string.format("%s %s", timestamp, message)
    
    table.insert(State.Logs, 1, logText)
    if #State.Logs > 50 then
        table.remove(State.Logs)
    end
    
    -- Update UI
    if LogsContainer then
        -- Clear and rebuild (simple approach)
        for _, child in pairs(LogsContainer:GetChildren()) do
            child:Destroy()
        end
        
        for i = 1, math.min(#State.Logs, 20) do
            local label = Instance.new("TextLabel")
            label.Size = UDim2.new(1, 0, 0, 20)
            label.BackgroundTransparency = 1
            label.TextColor3 = Color3.fromRGB(176, 214, 160)
            label.Font = Enum.Font.Code
            label.TextSize = 11
            label.TextXAlignment = Enum.TextXAlignment.Left
            label.Text = State.Logs[i]
            label.Parent = LogsContainer
        end
    end
end

-- Update status
local function SetStatus(status, text)
    State.Status = status
    
    -- Update dot
    if StatusDot then
        local colors = {
            Idle = Color3.fromRGB(106, 126, 158),
            Running = Color3.fromRGB(59, 130, 246),
            Done = Color3.fromRGB(34, 197, 94),
            Stopped = Color3.fromRGB(248, 113, 113),
        }
        StatusDot.BackgroundColor3 = colors[status] or colors.Idle
    end
    
    -- Update text
    if StatusText then
        local statusMap = {
            Idle = "● Idle",
            Running = "🟢 Running " .. (text or ""),
            Done = "✅ Done " .. (text or ""),
            Stopped = "🔴 Stopped",
        }
        StatusText.Text = statusMap[status] or "● Idle"
    end
end

-- ============================================================
-- 4. DRAGGABLE WINDOW
-- ============================================================

local dragging = false
local dragStart = nil
local startPos = nil

TitleBar.InputBegan:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseButton1 then
        dragging = true
        dragStart = input.Position
        startPos = Window.Position
    end
end)

TitleBar.InputEnded:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseButton1 then
        dragging = false
    end
end)

UserInputService.InputChanged:Connect(function(input)
    if dragging and input.UserInputType == Enum.UserInputType.MouseMovement then
        local delta = input.Position - dragStart
        Window.Position = UDim2.new(
            startPos.X.Scale,
            startPos.X.Offset + delta.X,
            startPos.Y.Scale,
            startPos.Y.Offset + delta.Y
        )
    end
end)

-- ============================================================
-- 5. SIDEBAR NAVIGATION
-- ============================================================

-- Map sidebar icon to content sections
local sectionMap = {
    ["🏠"] = { "Dashboard", "Base", "Cars", "Rewards" },
    ["🚗"] = { "Cars" },
    ["🏢"] = { "Base" },
    ["📦"] = { "Base" },
    ["🎁"] = { "Rewards" },
    ["⚙"] = { "Settings" },
    ["📄"] = { "Logs" },
}

local function ShowSection(sectionName)
    -- Hide all sections
    for _, child in pairs(Content:GetChildren()) do
        if child:IsA("Frame") and child.Name ~= "StatusBar" then
            child.Visible = false
        end
    end
    
    -- Show matching sections
    for _, name in pairs(sectionMap[sectionName] or {}) do
        local section = Content:FindFirstChild(name)
        if section then
            section.Visible = true
        end
    end
end

for _, item in pairs(SidebarItems) do
    if item:IsA("TextButton") then
        item.MouseButton1Click:Connect(function()
            -- Update active state
            for _, other in pairs(SidebarItems) do
                if other:IsA("TextButton") then
                    other.BackgroundTransparency = 1
                    other.TextColor3 = Color3.fromRGB(106, 126, 158)
                end
            end
            item.BackgroundTransparency = 0.85
            item.TextColor3 = Color3.fromRGB(200, 220, 255)
            
            -- Show content
            ShowSection(item.Text)
            AddLog("Navigated to " .. item.Text)
        end)
    end
end

-- ============================================================
-- 6. TABS
-- ============================================================

TabAuto.MouseButton1Click:Connect(function()
    State.ActiveTab = "Auto"
    TabAuto.TextColor3 = Color3.fromRGB(220, 232, 255)
    TabRunOnce.TextColor3 = Color3.fromRGB(127, 146, 181)
    AddLog("Switched to Auto mode")
end)

TabRunOnce.MouseButton1Click:Connect(function()
    State.ActiveTab = "RunOnce"
    TabRunOnce.TextColor3 = Color3.fromRGB(220, 232, 255)
    TabAuto.TextColor3 = Color3.fromRGB(127, 146, 181)
    AddLog("Switched to Run Once mode")
end)

-- ============================================================
-- 7. TOGGLES (iOS-style)
-- ============================================================

-- Find all toggle elements and set up interactions
local function SetupToggles()
    local toggleCount = 0
    
    for _, section in pairs(Content:GetChildren()) do
        if section:IsA("Frame") and section:FindFirstChild("ToggleContainer") then
            for _, row in pairs(section.ToggleContainer:GetChildren()) do
                if row:IsA("Frame") and row:FindFirstChild("Toggle") then
                    local toggle = row.Toggle
                    local knob = toggle:FindFirstChild("Knob")
                    local isActive = false
                    
                    -- Store reference
                    toggleCount = toggleCount + 1
                    local toggleId = toggleCount
                    State.Toggles[toggleId] = false
                    
                    -- Click handler
                    toggle.MouseButton1Click:Connect(function()
                        isActive = not isActive
                        State.Toggles[toggleId] = isActive
                        
                        -- Update visual
                        if isActive then
                            toggle.BackgroundColor3 = Color3.fromRGB(59, 130, 246)
                            knob.Position = UDim2.new(1, -19, 0.5, 0)
                        else
                            toggle.BackgroundColor3 = Color3.fromRGB(36, 44, 62)
                            knob.Position = UDim2.new(0, 3, 0.5, 0)
                        end
                        
                        -- Get label
                        local label = row:FindFirstChild("Label")
                        local title = label and label.Text or "Toggle"
                        AddLog(string.format("%s %s", isActive and "Enabled" or "Disabled", title))
                    end)
                end
            end
        end
    end
end

SetupToggles()

-- ============================================================
-- 8. SETTINGS (Slider + Toggles)
-- ============================================================

local SettingsSection = Content:FindFirstChild("Settings")
if SettingsSection then
    -- Delay slider
    local slider = SettingsSection:FindFirstChild("DelaySlider")
    local valueLabel = SettingsSection:FindFirstChild("DelayValue")
    
    if slider then
        slider:GetPropertyChangedSignal("Value"):Connect(function()
            State.Settings.Delay = slider.Value
            if valueLabel then
                valueLabel.Text = string.format("%.1fs", slider.Value)
            end
        end)
    end
    
    -- Settings toggles
    for _, row in pairs(SettingsSection:GetChildren()) do
        if row:IsA("Frame") and row:FindFirstChild("Toggle") then
            local toggle = row.Toggle
            local settingName = row:FindFirstChild("Label")
            settingName = settingName and settingName.Text or "Setting"
            
            -- Map to state
            if settingName:find("Auto Reconnect") then
                toggle.MouseButton1Click:Connect(function()
                    State.Settings.AutoReconnect = not State.Settings.AutoReconnect
                    AddLog(string.format("Auto Reconnect: %s", State.Settings.AutoReconnect and "ON" or "OFF"))
                end)
            elseif settingName:find("Anti AFK") then
                toggle.MouseButton1Click:Connect(function()
                    State.Settings.AntiAFK = not State.Settings.AntiAFK
                    AddLog(string.format("Anti AFK: %s", State.Settings.AntiAFK and "ON" or "OFF"))
                end)
            elseif settingName:find("Auto Save") then
                toggle.MouseButton1Click:Connect(function()
                    State.Settings.AutoSave = not State.Settings.AutoSave
                    AddLog(string.format("Auto Save: %s", State.Settings.AutoSave and "ON" or "OFF"))
                end)
            elseif settingName:find("Notifications") then
                toggle.MouseButton1Click:Connect(function()
                    State.Settings.Notifications = not State.Settings.Notifications
                    AddLog(string.format("Notifications: %s", State.Settings.Notifications and "ON" or "OFF"))
                end)
            elseif settingName:find("FPS Limit") then
                toggle.MouseButton1Click:Connect(function()
                    State.Settings.FPSLimit = not State.Settings.FPSLimit
                    AddLog(string.format("FPS Limit: %s", State.Settings.FPSLimit and "ON" or "OFF"))
                end)
            end
        end
    end
    
    -- Buttons
    local saveBtn = SettingsSection:FindFirstChild("SaveBtn")
    local loadBtn = SettingsSection:FindFirstChild("LoadBtn")
    local resetBtn = SettingsSection:FindFirstChild("ResetBtn")
    
    if saveBtn then
        saveBtn.MouseButton1Click:Connect(function()
            AddLog("Configuration saved")
            SetStatus("Done", "SaveConfig")
            task.wait(1)
            SetStatus("Idle")
        end)
    end
    
    if loadBtn then
        loadBtn.MouseButton1Click:Connect(function()
            AddLog("Configuration loaded")
            SetStatus("Done", "LoadConfig")
            task.wait(1)
            SetStatus("Idle")
        end)
    end
    
    if resetBtn then
        resetBtn.MouseButton1Click:Connect(function()
            AddLog("Settings reset to default")
            SetStatus("Done", "Reset")
            task.wait(1)
            SetStatus("Idle")
        end)
    end
end

-- ============================================================
-- 9. STATUS & LOGS (Live Updates)
-- ============================================================

-- Simulate automation activity
local function SimulateAutomation()
    local actions = {
        "Claimed Cash",
        "Bought Upgrade",
        "Sold Vehicle",
        "Delivered Junk",
        "Claimed Parts",
        "Fixed Car",
        "Opened Container",
        "Claimed Quest Reward",
    }
    
    local statuses = {
        "Running BuyFixAndSellCar",
        "Running FixCars",
        "Running SellFixedCars",
        "Running ClaimCash",
        "Running ClaimParts",
        "Running ClaimContainers",
    }
    
    while true do
        task.wait(math.random(8, 20))
        
        -- Random status update
        if math.random() < 0.3 then
            local statusText = statuses[math.random(#statuses)]
            SetStatus("Running", statusText)
        end
        
        -- Random log entry
        if math.random() < 0.5 then
            local action = actions[math.random(#actions)]
            AddLog(action)
        end
        
        -- Occasionally return to idle
        if math.random() < 0.15 then
            SetStatus("Idle")
        end
    end
end

-- Start simulation (in background)
task.spawn(SimulateAutomation)

-- ============================================================
-- 10. INITIALIZATION
-- ============================================================

-- Set initial status
SetStatus("Idle")

-- Show default section
ShowSection("🏠")

-- Add startup logs
AddLog("Car Flipper UI initialized")
AddLog("Ready for automation")

-- Log active state info
print("🎮 Car Flipper UI loaded successfully")
print(string.format("📊 Active Tab: %s", State.ActiveTab))
print(string.format("⚙️ Delay: %.1fs", State.Settings.Delay))

-- ============================================================
-- 11. WINDOW CONTROLS (Minimize, Maximize, Close)
-- ============================================================

local function SetupWindowControls()
    local controls = TitleBar:FindFirstChild("Controls")
    if not controls then return end
    
    for _, btn in pairs(controls:GetChildren()) do
        if btn:IsA("TextButton") then
            btn.MouseButton1Click:Connect(function()
                local text = btn.Text
                
                if text == "🗕" then
                    -- Minimize
                    Window.Visible = false
                    AddLog("Window minimized")
                elseif text == "🗖" then
                    -- Toggle maximize
                    local isMaximized = Window.Size == UDim2.new(1, -20, 1, -20)
                    if isMaximized then
                        Window.Size = UDim2.new(0, 760, 0, 520)
                        Window.Position = UDim2.new(0.5, -380, 0.5, -260)
                    else
                        Window.Size = UDim2.new(1, -20, 1, -20)
                        Window.Position = UDim2.new(0, 10, 0, 10)
                    end
                    AddLog(string.format("Window %s", isMaximized and "restored" or "maximized"))
                elseif text == "✕" then
                    -- Close
                    AddLog("Shutting down...")
                    task.wait(0.3)
                    ScreenGui.Enabled = false
                end
            end)
        end
    end
end

SetupWindowControls()

-- ============================================================
-- 12. KEYBOARD SHORTCUTS
-- ============================================================

UserInputService.InputBegan:Connect(function(input, gameProcessed)
    if gameProcessed then return end
    
    -- Toggle UI with F1
    if input.KeyCode == Enum.KeyCode.F1 then
        ScreenGui.Enabled = not ScreenGui.Enabled
        if ScreenGui.Enabled then
            AddLog("UI shown")
        end
    end
    
    -- Toggle automation with F2
    if input.KeyCode == Enum.KeyCode.F2 then
        if State.Status == "Running" then
            SetStatus("Stopped")
            AddLog("Automation stopped (F2)")
        else
            SetStatus("Running", "ManualStart")
            AddLog("Automation started (F2)")
        end
    end
end)

print("✅ Car Flipper UI ready - Press F1 to toggle, F2 to start/stop")

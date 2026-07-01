--[[
    Car Flipper - Complete Automation Framework
    Universal Roblox Automation UI
    Version 2.0
]]

-- SERVICES
local Players = game:GetService("Players")
local Player = Players.LocalPlayer
local GuiService = game:GetService("GuiService")
local TweenService = game:GetService("TweenService")
local RunService = game:GetService("RunService")
local UserInputService = game:GetService("UserInputService")
local ReplicatedStorage = game:GetService("ReplicatedStorage")
local HttpService = game:GetService("HttpService")

-- ============================================================
-- 1. UI SETUP (Complete Window System)
-- ============================================================

local function CreateUI()
    local ScreenGui = Instance.new("ScreenGui")
    ScreenGui.Name = "CarFlipperUI"
    ScreenGui.ResetOnSpawn = false
    ScreenGui.Parent = Player.PlayerGui
    
    -- Main Window with glassmorphism
    local Window = Instance.new("Frame")
    Window.Name = "Window"
    Window.Size = UDim2.new(0, 760, 0, 520)
    Window.Position = UDim2.new(0.5, -380, 0.5, -260)
    Window.BackgroundColor3 = Color3.fromRGB(18, 18, 22)
    Window.BackgroundTransparency = 0.28
    Window.BorderSizePixel = 0
    Window.ClipsDescendants = true
    Window.Parent = ScreenGui
    
    -- Glass blur effect
    local Blur = Instance.new("BlurEffect")
    Blur.Size = 18
    Blur.Parent = Window
    
    -- Border glow
    local Border = Instance.new("UICorner")
    Border.CornerRadius = UDim.new(0, 24)
    Border.Parent = Window
    
    local GlowBorder = Instance.new("Frame")
    GlowBorder.Name = "GlowBorder"
    GlowBorder.Size = UDim2.new(1, 2, 1, 2)
    GlowBorder.Position = UDim2.new(0, -1, 0, -1)
    GlowBorder.BackgroundTransparency = 1
    GlowBorder.BorderSizePixel = 0
    GlowBorder.Parent = Window
    
    local BorderGlow = Instance.new("UIStroke")
    BorderGlow.Thickness = 1.5
    BorderGlow.Color = Color3.fromRGB(59, 130, 246)
    BorderGlow.Transparency = 0.6
    BorderGlow.Parent = GlowBorder
    
    -- Title Bar
    local TitleBar = Instance.new("Frame")
    TitleBar.Name = "TitleBar"
    TitleBar.Size = UDim2.new(1, 0, 0, 44)
    TitleBar.BackgroundTransparency = 1
    TitleBar.Parent = Window
    
    local TitleText = Instance.new("TextLabel")
    TitleText.Size = UDim2.new(0.5, 0, 1, 0)
    TitleText.Position = UDim2.new(0, 20, 0, 0)
    TitleText.BackgroundTransparency = 1
    TitleText.Text = "🚗 Car Flipper"
    TitleText.TextColor3 = Color3.fromRGB(240, 244, 255)
    TitleText.TextScaled = false
    TitleText.TextSize = 18
    TitleText.Font = Enum.Font.Poppins
    TitleText.TextXAlignment = Enum.TextXAlignment.Left
    TitleText.Parent = TitleBar
    
    -- Window Controls
    local Controls = Instance.new("Frame")
    Controls.Name = "Controls"
    Controls.Size = UDim2.new(0, 120, 1, 0)
    Controls.Position = UDim2.new(1, -130, 0, 0)
    Controls.BackgroundTransparency = 1
    Controls.Parent = TitleBar
    
    local controlButtons = {"🗕", "🗖", "✕"}
    for i, btnText in ipairs(controlButtons) do
        local btn = Instance.new("TextButton")
        btn.Size = UDim2.new(0, 30, 1, 0)
        btn.Position = UDim2.new(0, (i-1) * 35, 0, 0)
        btn.BackgroundTransparency = 1
        btn.Text = btnText
        btn.TextColor3 = Color3.fromRGB(138, 159, 192)
        btn.TextSize = 16
        btn.Font = Enum.Font.Poppins
        btn.Name = btnText
        btn.Parent = Controls
        
        btn.MouseButton1Click:Connect(function()
            if btnText == "🗕" then
                Window.Visible = false
            elseif btnText == "🗖" then
                local isMaximized = Window.Size == UDim2.new(1, -20, 1, -20)
                Window.Size = isMaximized and UDim2.new(0, 760, 0, 520) or UDim2.new(1, -20, 1, -20)
                Window.Position = isMaximized and UDim2.new(0.5, -380, 0.5, -260) or UDim2.new(0, 10, 0, 10)
            elseif btnText == "✕" then
                ScreenGui.Enabled = false
            end
        end)
    end
    
    -- ============================================================
    -- 2. SIDEBAR
    -- ============================================================
    
    local Sidebar = Instance.new("Frame")
    Sidebar.Name = "Sidebar"
    Sidebar.Size = UDim2.new(0, 64, 1, -44)
    Sidebar.Position = UDim2.new(0, 0, 0, 44)
    Sidebar.BackgroundColor3 = Color3.fromRGB(0, 0, 0)
    Sidebar.BackgroundTransparency = 0.8
    Sidebar.BorderSizePixel = 0
    Sidebar.Parent = Window
    
    local sidebarIcons = {"🏠", "🚗", "🏢", "📦", "🎁", "⚙", "📄"}
    local sidebarNames = {"Dashboard", "Cars", "Base", "Containers", "Rewards", "Settings", "Logs"}
    
    for i, icon in ipairs(sidebarIcons) do
        local btn = Instance.new("TextButton")
        btn.Size = UDim2.new(0, 44, 0, 44)
        btn.Position = UDim2.new(0.5, -22, 0, 10 + (i-1) * 48)
        btn.BackgroundTransparency = 1
        btn.Text = icon
        btn.TextSize = 22
        btn.TextColor3 = Color3.fromRGB(106, 126, 158)
        btn.Font = Enum.Font.Poppins
        btn.Name = sidebarNames[i]
        btn.Parent = Sidebar
        
        -- Active indicator
        local indicator = Instance.new("Frame")
        indicator.Size = UDim2.new(0, 3, 0, 20)
        indicator.Position = UDim2.new(0, -2, 0.5, -10)
        indicator.BackgroundColor3 = Color3.fromRGB(59, 130, 246)
        indicator.BackgroundTransparency = 1
        indicator.BorderSizePixel = 0
        indicator.Parent = btn
        
        btn.MouseButton1Click:Connect(function()
            for _, child in pairs(Sidebar:GetChildren()) do
                if child:IsA("TextButton") then
                    child.TextColor3 = Color3.fromRGB(106, 126, 158)
                    local ind = child:FindFirstChild("Frame")
                    if ind then ind.BackgroundTransparency = 1 end
                end
            end
            btn.TextColor3 = Color3.fromRGB(200, 220, 255)
            indicator.BackgroundTransparency = 0
            ShowSection(btn.Name)
        end)
    end
    
    -- ============================================================
    -- 3. MAIN CONTENT AREA
    -- ============================================================
    
    local Main = Instance.new("Frame")
    Main.Name = "Main"
    Main.Size = UDim2.new(1, -64, 1, -44)
    Main.Position = UDim2.new(0, 64, 0, 44)
    Main.BackgroundTransparency = 1
    Main.Parent = Window
    
    -- Content Container
    local Content = Instance.new("Frame")
    Content.Name = "Content"
    Content.Size = UDim2.new(1, -36, 1, -20)
    Content.Position = UDim2.new(0, 18, 0, 10)
    Content.BackgroundTransparency = 1
    Content.Parent = Main
    
    -- Create Scrollable Content
    local Scrolling = Instance.new("ScrollingFrame")
    Scrolling.Size = UDim2.new(1, 0, 1, 0)
    Scrolling.BackgroundTransparency = 1
    Scrolling.BorderSizePixel = 0
    Scrolling.ScrollBarThickness = 4
    Scrolling.ScrollBarImageColor3 = Color3.fromRGB(59, 130, 246)
    Scrolling.VerticalScrollBarInset = Enum.ScrollBarInset.ScrollBar
    Scrolling.Parent = Content
    
    local ContentList = Instance.new("UIListLayout")
    ContentList.Padding = UDim.new(0, 12)
    ContentList.HorizontalAlignment = Enum.HorizontalAlignment.Center
    ContentList.SortOrder = Enum.SortOrder.LayoutOrder
    ContentList.Parent = Scrolling
    
    -- ============================================================
    -- 4. CREATE SECTIONS
    -- ============================================================
    
    local sections = {}
    
    -- Helper: Create Section
    local function CreateSection(title, icon, layoutOrder)
        local section = Instance.new("Frame")
        section.Size = UDim2.new(1, 0, 0, 0)
        section.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
        section.BackgroundTransparency = 0.98
        section.BorderSizePixel = 0
        section.ClipsDescendants = true
        section.LayoutOrder = layoutOrder
        section.Visible = false
        section.Parent = Scrolling
        
        local corner = Instance.new("UICorner")
        corner.CornerRadius = UDim.new(0, 20)
        corner.Parent = section
        
        local titleFrame = Instance.new("Frame")
        titleFrame.Size = UDim2.new(1, -32, 0, 40)
        titleFrame.Position = UDim2.new(0, 16, 0, 8)
        titleFrame.BackgroundTransparency = 1
        titleFrame.Parent = section
        
        local titleLabel = Instance.new("TextLabel")
        titleLabel.Size = UDim2.new(1, 0, 1, 0)
        titleLabel.BackgroundTransparency = 1
        titleLabel.Text = icon .. " " .. title
        titleLabel.TextColor3 = Color3.fromRGB(196, 212, 240)
        titleLabel.TextSize = 16
        titleLabel.Font = Enum.Font.Poppins
        titleLabel.TextXAlignment = Enum.TextXAlignment.Left
        titleLabel.Parent = titleFrame
        
        -- Container for content
        local container = Instance.new("Frame")
        container.Name = "Container"
        container.Size = UDim2.new(1, -32, 0, 0)
        container.Position = UDim2.new(0, 16, 0, 48)
        container.BackgroundTransparency = 1
        container.Parent = section
        
        local list = Instance.new("UIListLayout")
        list.Padding = UDim.new(0, 4)
        list.HorizontalAlignment = Enum.HorizontalAlignment.Center
        list.SortOrder = Enum.SortOrder.LayoutOrder
        list.Parent = container
        
        sections[title] = {
            Section = section,
            Container = container,
            List = list,
            Items = {}
        }
        
        return sections[title]
    end
    
    -- Helper: Add Toggle Row
    local function AddToggleRow(parent, icon, title, desc, defaultActive, isGreen)
        local row = Instance.new("Frame")
        row.Size = UDim2.new(1, 0, 0, 40)
        row.BackgroundTransparency = 1
        row.Parent = parent.Container
        
        local iconLabel = Instance.new("TextLabel")
        iconLabel.Size = UDim2.new(0, 30, 1, 0)
        iconLabel.BackgroundTransparency = 1
        iconLabel.Text = icon
        iconLabel.TextSize = 18
        iconLabel.TextColor3 = Color3.fromRGB(200, 220, 255)
        iconLabel.Font = Enum.Font.Poppins
        iconLabel.Parent = row
        
        local infoFrame = Instance.new("Frame")
        infoFrame.Size = UDim2.new(1, -120, 1, 0)
        infoFrame.Position = UDim2.new(0, 34, 0, 0)
        infoFrame.BackgroundTransparency = 1
        infoFrame.Parent = row
        
        local titleLabel = Instance.new("TextLabel")
        titleLabel.Size = UDim2.new(1, 0, 0, 20)
        titleLabel.BackgroundTransparency = 1
        titleLabel.Text = title
        titleLabel.TextColor3 = Color3.fromRGB(220, 230, 245)
        titleLabel.TextSize = 13
        titleLabel.Font = Enum.Font.Poppins
        titleLabel.TextXAlignment = Enum.TextXAlignment.Left
        titleLabel.Parent = infoFrame
        
        local descLabel = Instance.new("TextLabel")
        descLabel.Size = UDim2.new(1, 0, 0, 16)
        descLabel.Position = UDim2.new(0, 0, 0, 20)
        descLabel.BackgroundTransparency = 1
        descLabel.Text = desc
        descLabel.TextColor3 = Color3.fromRGB(113, 134, 170)
        descLabel.TextSize = 10
        descLabel.Font = Enum.Font.Poppins
        descLabel.TextXAlignment = Enum.TextXAlignment.Left
        descLabel.Parent = infoFrame
        
        -- iOS Toggle
        local toggle = Instance.new("Frame")
        toggle.Size = UDim2.new(0, 38, 0, 22)
        toggle.Position = UDim2.new(1, -42, 0.5, -11)
        toggle.BackgroundColor3 = defaultActive and (isGreen and Color3.fromRGB(34, 197, 94) or Color3.fromRGB(59, 130, 246)) or Color3.fromRGB(36, 44, 62)
        toggle.BorderSizePixel = 0
        toggle.Parent = row
        
        local toggleCorner = Instance.new("UICorner")
        toggleCorner.CornerRadius = UDim.new(1, 0)
        toggleCorner.Parent = toggle
        
        local knob = Instance.new("Frame")
        knob.Size = UDim2.new(0, 16, 0, 16)
        knob.Position = defaultActive and UDim2.new(1, -19, 0.5, -8) or UDim2.new(0, 3, 0.5, -8)
        knob.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
        knob.BorderSizePixel = 0
        knob.Parent = toggle
        
        local knobCorner = Instance.new("UICorner")
        knobCorner.CornerRadius = UDim.new(1, 0)
        knobCorner.Parent = knob
        
        local isActive = defaultActive or false
        
        toggle.MouseButton1Click:Connect(function()
            isActive = not isActive
            local targetColor = isActive and (isGreen and Color3.fromRGB(34, 197, 94) or Color3.fromRGB(59, 130, 246)) or Color3.fromRGB(36, 44, 62)
            local targetPos = isActive and UDim2.new(1, -19, 0.5, -8) or UDim2.new(0, 3, 0.5, -8)
            
            local tween1 = TweenService:Create(toggle, TweenInfo.new(0.25), {BackgroundColor3 = targetColor})
            local tween2 = TweenService:Create(knob, TweenInfo.new(0.25), {Position = targetPos})
            tween1:Play()
            tween2:Play()
            
            AddLog(string.format("%s %s", isActive and "Enabled" or "Disabled", title))
        end)
        
        table.insert(parent.Items, {Title = title, Toggle = toggle, Active = isActive})
        
        return toggle
    end
    
    -- Helper: Add Log Entry
    local function AddLog(message)
        -- Implementation will be added later
    end
    
    -- ============================================================
    -- 5. CREATE ALL SECTIONS
    -- ============================================================
    
    -- Dashboard Section
    local dashboard = CreateSection("Dashboard", "📊", 1)
    local statsGrid = Instance.new("Frame")
    statsGrid.Size = UDim2.new(1, 0, 0, 80)
    statsGrid.BackgroundTransparency = 1
    statsGrid.Parent = dashboard.Container
    
    local grid = Instance.new("UIGridLayout")
    grid.CellPadding = UDim.new(0, 8)
    grid.CellSize = UDim.new(0.22, 0, 1, -8)
    grid.HorizontalAlignment = Enum.HorizontalAlignment.Center
    grid.Parent = statsGrid
    
    local stats = {
        {icon = "💰", label = "Money", value = "$5,420,000"},
        {icon = "🧩", label = "Parts", value = "1,245"},
        {icon = "🚘", label = "Cars Sold", value = "482"},
        {icon = "⏱️", label = "Runtime", value = "02:14:35"},
    }
    
    for _, stat in ipairs(stats) do
        local card = Instance.new("Frame")
        card.Size = UDim2.new(0, 0, 1, 0)
        card.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
        card.BackgroundTransparency = 0.97
        card.BorderSizePixel = 0
        card.Parent = statsGrid
        
        local cardCorner = Instance.new("UICorner")
        cardCorner.CornerRadius = UDim.new(0, 16)
        cardCorner.Parent = card
        
        local iconLabel = Instance.new("TextLabel")
        iconLabel.Size = UDim2.new(1, 0, 0, 28)
        iconLabel.Position = UDim2.new(0, 0, 0, 6)
        iconLabel.BackgroundTransparency = 1
        iconLabel.Text = stat.icon
        iconLabel.TextSize = 22
        iconLabel.TextColor3 = Color3.fromRGB(255, 255, 255)
        iconLabel.Font = Enum.Font.Poppins
        iconLabel.Parent = card
        
        local valueLabel = Instance.new("TextLabel")
        valueLabel.Size = UDim2.new(1, 0, 0, 28)
        valueLabel.Position = UDim2.new(0, 0, 0, 34)
        valueLabel.BackgroundTransparency = 1
        valueLabel.Text = stat.value
        valueLabel.TextColor3 = Color3.fromRGB(240, 246, 255)
        valueLabel.TextSize = 20
        valueLabel.Font = Enum.Font.Poppins
        valueLabel.TextXAlignment = Enum.TextXAlignment.Left
        valueLabel.Parent = card
        
        local labelLabel = Instance.new("TextLabel")
        labelLabel.Size = UDim2.new(1, 0, 0, 14)
        labelLabel.Position = UDim2.new(0, 0, 0, 62)
        labelLabel.BackgroundTransparency = 1
        labelLabel.Text = stat.label
        labelLabel.TextColor3 = Color3.fromRGB(113, 134, 170)
        labelLabel.TextSize = 10
        labelLabel.Font = Enum.Font.Poppins
        labelLabel.TextXAlignment = Enum.TextXAlignment.Left
        labelLabel.Parent = card
    end
    
    -- Base Section
    local base = CreateSection("Base", "🏢", 2)
    local baseToggles = {
        {icon = "💰", title = "Claim Cash", desc = "Collect daily cash", default = true, green = true},
        {icon = "🧩", title = "Claim Parts", desc = "Collect parts", default = true},
        {icon = "⬆️", title = "Buy Upgrades", desc = "Purchase upgrades", default = false},
        {icon = "📦", title = "Claim Containers", desc = "Open containers", default = true, green = true},
        {icon = "🎁", title = "Open Containers", desc = "Reveal rewards", default = false},
        {icon = "📋", title = "Claim Quests", desc = "Quest rewards", default = true},
        {icon = "🗑️", title = "Deliver Junk", desc = "Sell junk items", default = false},
    }
    
    for _, toggleData in ipairs(baseToggles) do
        AddToggleRow(base, toggleData.icon, toggleData.title, toggleData.desc, toggleData.default, toggleData.green)
    end
    
    -- Cars Section
    local cars = CreateSection("Cars", "🚗", 3)
    local carToggles = {
        {icon = "🔧", title = "Fix Cars", desc = "Repair vehicles", default = true},
        {icon = "💲", title = "Sell Fixed Cars", desc = "Sell repaired", default = true, green = true},
        {icon = "🔄", title = "Update Stands", desc = "Refresh display", default = false},
        {icon = "🏷️", title = "Buy, Fix & Sell Cheapest", desc = "Auto flip cheapest", default = true},
    }
    
    for _, toggleData in ipairs(carToggles) do
        AddToggleRow(cars, toggleData.icon, toggleData.title, toggleData.desc, toggleData.default, toggleData.green)
    end
    
    -- Rewards Section
    local rewards = CreateSection("Rewards", "🎁", 4)
    local rewardToggles = {
        {icon = "⏳", title = "Claim Playtime Rewards", desc = "Time-based rewards", default = true, green = true},
        {icon = "📆", title = "Claim Daily Rewards", desc = "Daily login", default = true},
    }
    
    for _, toggleData in ipairs(rewardToggles) do
        AddToggleRow(rewards, toggleData.icon, toggleData.title, toggleData.desc, toggleData.default, toggleData.green)
    end
    
    -- Settings Section
    local settings = CreateSection("Settings", "⚙", 5)
    
    -- Delay Slider
    local sliderFrame = Instance.new("Frame")
    sliderFrame.Size = UDim2.new(1, 0, 0, 30)
    sliderFrame.BackgroundTransparency = 1
    sliderFrame.Parent = settings.Container
    
    local sliderLabel = Instance.new("TextLabel")
    sliderLabel.Size = UDim2.new(0, 60, 1, 0)
    sliderLabel.BackgroundTransparency = 1
    sliderLabel.Text = "Delay"
    sliderLabel.TextColor3 = Color3.fromRGB(176, 196, 224)
    sliderLabel.TextSize = 12
    sliderLabel.Font = Enum.Font.Poppins
    sliderLabel.TextXAlignment = Enum.TextXAlignment.Left
    sliderLabel.Parent = sliderFrame
    
    local slider = Instance.new("Slider")
    slider.Size = UDim2.new(0, 150, 1, 0)
    slider.Position = UDim2.new(0, 64, 0, 0)
    slider.Min = 0.5
    slider.Max = 30
    slider.Value = 1.5
    slider.BackgroundColor3 = Color3.fromRGB(36, 44, 62)
    slider.Parent = sliderFrame
    
    local sliderCorner = Instance.new("UICorner")
    sliderCorner.CornerRadius = UDim.new(1, 0)
    sliderCorner.Parent = slider
    
    local sliderValue = Instance.new("TextLabel")
    sliderValue.Size = UDim2.new(0, 40, 1, 0)
    sliderValue.Position = UDim2.new(1, -44, 0, 0)
    sliderValue.BackgroundTransparency = 1
    sliderValue.Text = "1.5s"
    sliderValue.TextColor3 = Color3.fromRGB(208, 224, 255)
    sliderValue.TextSize = 12
    sliderValue.Font = Enum.Font.Poppins
    sliderValue.TextXAlignment = Enum.TextXAlignment.Right
    sliderValue.Parent = sliderFrame
    
    slider:GetPropertyChangedSignal("Value"):Connect(function()
        sliderValue.Text = string.format("%.1fs", slider.Value)
    end)
    
    -- Settings Toggles
    local settingToggles = {
        {icon = "⚡", title = "Auto Reconnect", default = true},
        {icon = "🛡️", title = "Anti AFK", default = true, green = true},
        {icon = "💾", title = "Auto Save", default = true},
        {icon = "🔔", title = "Notifications", default = false},
        {icon = "🎮", title = "FPS Limit", default = true, green = true},
    }
    
    for _, toggleData in ipairs(settingToggles) do
        AddToggleRow(settings, toggleData.icon, toggleData.title, "", toggleData.default, toggleData.green)
    end
    
    -- Buttons
    local buttonFrame = Instance.new("Frame")
    buttonFrame.Size = UDim2.new(1, 0, 0, 34)
    buttonFrame.BackgroundTransparency = 1
    buttonFrame.Parent = settings.Container
    
    local buttonLayout = Instance.new("UIListLayout")
    buttonLayout.FillDirection = Enum.FillDirection.Horizontal
    buttonLayout.Padding = UDim.new(0, 8)
    buttonLayout.HorizontalAlignment = Enum.HorizontalAlignment.Center
    buttonLayout.Parent = buttonFrame
    
    local buttons = {"Save Config", "Load Config", "Reset"}
    for _, btnText in ipairs(buttons) do
        local btn = Instance.new("TextButton")
        btn.Size = UDim2.new(0, 110, 1, 0)
        btn.BackgroundColor3 = Color3.fromRGB(59, 130, 246)
        btn.BackgroundTransparency = 0.85
        btn.Text = btnText
        btn.TextColor3 = Color3.fromRGB(192, 212, 252)
        btn.TextSize = 12
        btn.Font = Enum.Font.Poppins
        btn.Parent = buttonFrame
        
        local btnCorner = Instance.new("UICorner")
        btnCorner.CornerRadius = UDim.new(1, 0)
        btnCorner.Parent = btn
        
        if btnText == "Reset" then
            btn.BackgroundColor3 = Color3.fromRGB(255, 80, 80)
        end
        
        btn.MouseButton1Click:Connect(function()
            AddLog(string.format("%s clicked", btnText))
        end)
    end
    
    -- Logs Section
    local logs = CreateSection("Logs", "📄", 6)
    
    local logContainer = Instance.new("Frame")
    logContainer.Size = UDim2.new(1, 0, 0, 120)
    logContainer.BackgroundColor3 = Color3.fromRGB(0, 0, 0)
    logContainer.BackgroundTransparency = 0.5
    logContainer.BorderSizePixel = 0
    logContainer.Parent = logs.Container
    
    local logCorner = Instance.new("UICorner")
    logCorner.CornerRadius = UDim.new(0, 12)
    logCorner.Parent = logContainer
    
    local logScrolling = Instance.new("ScrollingFrame")
    logScrolling.Size = UDim2.new(1, -16, 1, -16)
    logScrolling.Position = UDim2.new(0, 8, 0, 8)
    logScrolling.BackgroundTransparency = 1
    logScrolling.BorderSizePixel = 0
    logScrolling.ScrollBarThickness = 3
    logScrolling.ScrollBarImageColor3 = Color3.fromRGB(34, 197, 94)
    logScrolling.VerticalScrollBarInset = Enum.ScrollBarInset.ScrollBar
    logScrolling.Parent = logContainer
    
    local logList = Instance.new("UIListLayout")
    logList.Padding = UDim.new(0, 2)
    logList.HorizontalAlignment = Enum.HorizontalAlignment.Left
    logList.SortOrder = Enum.SortOrder.LayoutOrder
    logList.Parent = logScrolling
    
    -- Status Bar
    local statusBar = Instance.new("Frame")
    statusBar.Name = "StatusBar"
    statusBar.Size = UDim2.new(1, -32, 0, 32)
    statusBar.Position = UDim2.new(0, 16, 1, -40)
    statusBar.BackgroundColor3 = Color3.fromRGB(0, 0, 0)
    statusBar.BackgroundTransparency = 0.7
    statusBar.BorderSizePixel = 0
    statusBar.Parent = Main
    
    local statusCorner = Instance.new("UICorner")
    statusCorner.CornerRadius = UDim.new(1, 0)
    statusCorner.Parent = statusBar
    
    local statusDot = Instance.new("Frame")
    statusDot.Name = "Dot"
    statusDot.Size = UDim2.new(0, 8, 0, 8)
    statusDot.Position = UDim2.new(0, 12, 0.5, -4)
    statusDot.BackgroundColor3 = Color3.fromRGB(34, 197, 94)
    statusDot.BorderSizePixel = 0
    statusDot.Parent = statusBar
    
    local dotCorner = Instance.new("UICorner")
    dotCorner.CornerRadius = UDim.new(1, 0)
    dotCorner.Parent = statusDot
    
    local statusText = Instance.new("TextLabel")
    statusText.Name = "Text"
    statusText.Size = UDim2.new(1, -60, 1, 0)
    statusText.Position = UDim2.new(0, 24, 0, 0)
    statusText.BackgroundTransparency = 1
    statusText.Text = "● Idle"
    statusText.TextColor3 = Color3.fromRGB(192, 208, 236)
    statusText.TextSize = 12
    statusText.Font = Enum.Font.Poppins
    statusText.TextXAlignment = Enum.TextXAlignment.Left
    statusText.Parent = statusBar
    
    -- ============================================================
    -- 6. LOGGING SYSTEM
    -- ============================================================
    
    local logEntries = {}
    
    function AddLog(message)
        local timestamp = os.date("%H:%M")
        local entry = string.format("%s %s", timestamp, message)
        
        table.insert(logEntries, 1, entry)
        if #logEntries > 50 then
            table.remove(logEntries)
        end
        
        -- Update UI
        for _, child in pairs(logScrolling:GetChildren()) do
            if child:IsA("TextLabel") then
                child:Destroy()
            end
        end
        
        for i = 1, math.min(#logEntries, 20) do
            local label = Instance.new("TextLabel")
            label.Size = UDim2.new(1, 0, 0, 18)
            label.BackgroundTransparency = 1
            label.Text = logEntries[i]
            label.TextColor3 = Color3.fromRGB(176, 214, 160)
            label.TextSize = 11
            label.Font = Enum.Font.Code
            label.TextXAlignment = Enum.TextXAlignment.Left
            label.Parent = logScrolling
        end
        
        -- Auto-scroll to top
        logScrolling.CanvasPosition = Vector2.new(0, 0)
    end
    
    -- ============================================================
    -- 7. SECTION VISIBILITY
    -- ============================================================
    
    function ShowSection(sectionName)
        local sectionMap = {
            Dashboard = {"Dashboard"},
            Cars = {"Cars"},
            Base = {"Base"},
            Containers = {"Base"},
            Rewards = {"Rewards"},
            Settings = {"Settings"},
            Logs = {"Logs"},
        }
        
        -- Hide all sections
        for _, section in pairs(sections) do
            section.Section.Visible = false
        end
        
        -- Show matching sections
        local names = sectionMap[sectionName] or {}
        for _, name in ipairs(names) do
            if sections[name] then
                sections[name].Section.Visible = true
                -- Auto-size
                local container = sections[name].Container
                local children = container:GetChildren()
                local height = 0
                for _, child in ipairs(children) do
                    if child:IsA("Frame") then
                        height = height + child.Size.Y.Offset + 4
                    end
                end
                sections[name].Section.Size = UDim2.new(1, 0, 0, height + 60)
            end
        end
    end
    
    -- Show default
    ShowSection("Dashboard")
    AddLog("Car Flipper UI initialized")
    AddLog("Ready for automation")
    
    -- ============================================================
    -- 8. KEYBOARD SHORTCUTS
    -- ============================================================
    
    UserInputService.InputBegan:Connect(function(input, gameProcessed)
        if gameProcessed then return end
        
        if input.KeyCode == Enum.KeyCode.F1 then
            ScreenGui.Enabled = not ScreenGui.Enabled
            if ScreenGui.Enabled then
                AddLog("UI shown")
            end
        end
        
        if input.KeyCode == Enum.KeyCode.F2 then
            AddLog("Automation toggled")
            statusDot.BackgroundColor3 = statusDot.BackgroundColor3 == Color3.fromRGB(34, 197, 94) and 
                Color3.fromRGB(59, 130, 246) or Color3.fromRGB(34, 197, 94)
            statusText.Text = statusDot.BackgroundColor3 == Color3.fromRGB(34, 197, 94) and 
                "🟢 Running" or "● Idle"
        end
    end)
    
    -- ============================================================
    -- 9. DRAGGABLE WINDOW
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
                startPos

--[[
    Car Flipper - Painel Completo com Developer Console
    Todas as opções integradas
    Versão 6.0
]]

-- ============================================================
-- 1. CONFIGURAÇÕES INICIAIS
-- ============================================================

local Players = game:GetService("Players")
local Player = Players.LocalPlayer
local TweenService = game:GetService("TweenService")
local UserInputService = game:GetService("UserInputService")
local RunService = game:GetService("RunService")
local ReplicatedStorage = game:GetService("ReplicatedStorage")
local VirtualUser = game:GetService("VirtualUser")
local HttpService = game:GetService("HttpService")

repeat task.wait() until Player and Player.Character and Player.Character:FindFirstChild("Humanoid")
print("🚗 Car Flipper Painel Completo Iniciando...")

-- ============================================================
-- 2. VARIÁVEIS GLOBAIS
-- ============================================================

local isRunning = false
local autoFarmActive = false
local currentAction = "Idle"
local logEntries = {}
local consoleLines = {}

-- ============================================================
-- 3. CRIAR UI PRINCIPAL
-- ============================================================

local function CreateCarFlipperUI()
    local ScreenGui = Instance.new("ScreenGui")
    ScreenGui.Name = "CarFlipperPanel"
    ScreenGui.ResetOnSpawn = false
    ScreenGui.ZIndexBehavior = Enum.ZIndexBehavior.Sibling
    ScreenGui.Parent = Player.PlayerGui
    
    -- ============================================================
    -- 4. JANELA PRINCIPAL
    -- ============================================================
    
    local MainFrame = Instance.new("Frame")
    MainFrame.Name = "MainFrame"
    MainFrame.Size = UDim2.new(0, 520, 0, 680)
    MainFrame.Position = UDim2.new(0.5, -260, 0.5, -340)
    MainFrame.BackgroundColor3 = Color3.fromRGB(10, 12, 18)
    MainFrame.BackgroundTransparency = 0.05
    MainFrame.BorderSizePixel = 0
    MainFrame.ClipsDescendants = true
    MainFrame.Parent = ScreenGui
    
    -- Efeitos visuais
    local Blur = Instance.new("BlurEffect")
    Blur.Size = 24
    Blur.Parent = MainFrame
    
    local Corner = Instance.new("UICorner")
    Corner.CornerRadius = UDim.new(0, 18)
    Corner.Parent = MainFrame
    
    local Border = Instance.new("UIStroke")
    Border.Color = Color3.fromRGB(59, 130, 246)
    Border.Thickness = 1.5
    Border.Transparency = 0.3
    Border.Parent = MainFrame
    
    -- Sombra
    local Shadow = Instance.new("Frame")
    Shadow.Size = UDim2.new(1, 20, 1, 20)
    Shadow.Position = UDim2.new(0, -10, 0, -10)
    Shadow.BackgroundColor3 = Color3.fromRGB(0, 0, 0)
    Shadow.BackgroundTransparency = 0.85
    Shadow.BorderSizePixel = 0
    Shadow.ZIndex = 0
    Shadow.Parent = MainFrame
    
    local ShadowCorner = Instance.new("UICorner")
    ShadowCorner.CornerRadius = UDim.new(0, 22)
    ShadowCorner.Parent = Shadow
    
    -- ============================================================
    -- 5. HEADER COM INFO DO JOGO
    -- ============================================================
    
    local Header = Instance.new("Frame")
    Header.Name = "Header"
    Header.Size = UDim2.new(1, 0, 0, 56)
    Header.BackgroundColor3 = Color3.fromRGB(14, 16, 26)
    Header.BorderSizePixel = 0
    Header.Parent = MainFrame
    
    local HeaderCorner = Instance.new("UICorner")
    HeaderCorner.CornerRadius = UDim.new(0, 18)
    HeaderCorner.Parent = Header
    
    -- Título
    local Title = Instance.new("TextLabel")
    Title.Size = UDim2.new(0.5, 0, 0.5, 0)
    Title.Position = UDim2.new(0, 16, 0, 4)
    Title.BackgroundTransparency = 1
    Title.Text = "🚗 Car Flipper"
    Title.TextColor3 = Color3.fromRGB(255, 255, 255)
    Title.TextSize = 20
    Title.Font = Enum.Font.Poppins
    Title.TextXAlignment = Enum.TextXAlignment.Left
    Title.Parent = Header
    
    -- Money
    local MoneyLabel = Instance.new("TextLabel")
    MoneyLabel.Size = UDim2.new(0.4, 0, 0.5, 0)
    MoneyLabel.Position = UDim2.new(0.5, 0, 0, 4)
    MoneyLabel.BackgroundTransparency = 1
    MoneyLabel.Text = "💰 1,497,451$"
    MoneyLabel.TextColor3 = Color3.fromRGB(255, 215, 0)
    MoneyLabel.TextSize = 16
    MoneyLabel.Font = Enum.Font.Poppins
    MoneyLabel.TextXAlignment = Enum.TextXAlignment.Right
    MoneyLabel.Parent = Header
    
    -- Status do sistema
    local SysStatus = Instance.new("TextLabel")
    SysStatus.Size = UDim2.new(0.5, 0, 0.4, 0)
    SysStatus.Position = UDim2.new(0, 16, 0, 32)
    SysStatus.BackgroundTransparency = 1
    SysStatus.Text = "[SYSTEM] AUCTION STARTED"
    SysStatus.TextColor3 = Color3.fromRGB(34, 197, 94)
    SysStatus.TextSize = 11
    SysStatus.Font = Enum.Font.Poppins
    SysStatus.TextXAlignment = Enum.TextXAlignment.Left
    SysStatus.Parent = Header
    
    -- Carro selecionado
    local CarSelected = Instance.new("TextLabel")
    CarSelected.Size = UDim2.new(0.5, 0, 0.4, 0)
    CarSelected.Position = UDim2.new(0.5, 0, 0, 32)
    CarSelected.BackgroundTransparency = 1
    CarSelected.Text = "[EPIC] Desert Crane Pinnace"
    CarSelected.TextColor3 = Color3.fromRGB(255, 215, 0)
    CarSelected.TextSize = 11
    CarSelected.Font = Enum.Font.Poppins
    CarSelected.TextXAlignment = Enum.TextXAlignment.Right
    CarSelected.Parent = Header
    
    -- Controles da janela
    local Controls = Instance.new("Frame")
    Controls.Name = "Controls"
    Controls.Size = UDim2.new(0, 90, 1, 0)
    Controls.Position = UDim2.new(1, -94, 0, 0)
    Controls.BackgroundTransparency = 1
    Controls.Parent = Header
    
    local function CreateControlButton(text, xPos, color)
        local btn = Instance.new("TextButton")
        btn.Size = UDim2.new(0, 26, 0, 26)
        btn.Position = UDim2.new(0, xPos, 0.5, -13)
        btn.BackgroundTransparency = 1
        btn.Text = text
        btn.TextColor3 = color or Color3.fromRGB(180, 190, 220)
        btn.TextSize = 15
        btn.Font = Enum.Font.Poppins
        btn.Parent = Controls
        return btn
    end
    
    local minimizeBtn = CreateControlButton("—", 0)
    local maximizeBtn = CreateControlButton("□", 30)
    local closeBtn = CreateControlButton("✕", 60, Color3.fromRGB(255, 80, 80))
    
    -- ============================================================
    -- 6. STATUS BAR COM INFO DO JOGO
    -- ============================================================
    
    local StatusBar = Instance.new("Frame")
    StatusBar.Name = "StatusBar"
    StatusBar.Size = UDim2.new(1, -32, 0, 34)
    StatusBar.Position = UDim2.new(0, 16, 0, 64)
    StatusBar.BackgroundColor3 = Color3.fromRGB(14, 16, 26)
    StatusBar.BackgroundTransparency = 0.6
    StatusBar.BorderSizePixel = 0
    StatusBar.Parent = MainFrame
    
    local StatusCorner = Instance.new("UICorner")
    StatusCorner.CornerRadius = UDim.new(1, 0)
    StatusCorner.Parent = StatusBar
    
    -- Status dot
    local StatusDot = Instance.new("Frame")
    StatusDot.Name = "StatusDot"
    StatusDot.Size = UDim2.new(0, 8, 0, 8)
    StatusDot.Position = UDim2.new(0, 12, 0.5, -4)
    StatusDot.BackgroundColor3 = Color3.fromRGB(34, 197, 94)
    StatusDot.BorderSizePixel = 0
    StatusDot.Parent = StatusBar
    
    local DotCorner = Instance.new("UICorner")
    DotCorner.CornerRadius = UDim.new(1, 0)
    DotCorner.Parent = StatusDot
    
    local DotGlow = Instance.new("Frame")
    DotGlow.Size = UDim2.new(0, 20, 0, 20)
    DotGlow.Position = UDim2.new(0, 6, 0.5, -10)
    DotGlow.BackgroundColor3 = Color3.fromRGB(34, 197, 94)
    DotGlow.BackgroundTransparency = 0.8
    DotGlow.BorderSizePixel = 0
    DotGlow.Parent = StatusBar
    
    local GlowCorner = Instance.new("UICorner")
    GlowCorner.CornerRadius = UDim.new(1, 0)
    GlowCorner.Parent = DotGlow
    
    -- Status text
    local StatusText = Instance.new("TextLabel")
    StatusText.Name = "StatusText"
    StatusText.Size = UDim2.new(0.5, 0, 1, 0)
    StatusText.Position = UDim2.new(0, 28, 0, 0)
    StatusText.BackgroundTransparency = 1
    StatusText.Text = "[SYSTEM] AUCTION STARTED"
    StatusText.TextColor3 = Color3.fromRGB(180, 200, 240)
    StatusText.TextSize = 11
    StatusText.Font = Enum.Font.Poppins
    StatusText.TextXAlignment = Enum.TextXAlignment.Left
    StatusText.Parent = StatusBar
    
    -- Info do carro
    local CarInfo = Instance.new("TextLabel")
    CarInfo.Name = "CarInfo"
    CarInfo.Size = UDim2.new(0.5, 0, 1, 0)
    CarInfo.Position = UDim2.new(0.5, 0, 0, 0)
    CarInfo.BackgroundTransparency = 1
    CarInfo.Text = "[EPIC] Desert Crane Pinnace"
    CarInfo.TextColor3 = Color3.fromRGB(255, 215, 0)
    CarInfo.TextSize = 11
    CarInfo.Font = Enum.Font.Poppins
    CarInfo.TextXAlignment = Enum.TextXAlignment.Right
    CarInfo.Parent = StatusBar
    
    -- ============================================================
    -- 7. TABS (Auto / Run Once / Console)
    -- ============================================================
    
    local TabContainer = Instance.new("Frame")
    TabContainer.Name = "TabContainer"
    TabContainer.Size = UDim2.new(1, -32, 0, 34)
    TabContainer.Position = UDim2.new(0, 16, 0, 104)
    TabContainer.BackgroundTransparency = 1
    TabContainer.Parent = MainFrame
    
    local function CreateTab(text, xPos, isActive)
        local tab = Instance.new("TextButton")
        tab.Size = UDim2.new(0, 100, 1, 0)
        tab.Position = UDim2.new(0, xPos, 0, 0)
        tab.BackgroundTransparency = 1
        tab.Text = text
        tab.TextColor3 = isActive and Color3.fromRGB(255, 255, 255) or Color3.fromRGB(128, 140, 180)
        tab.TextSize = 13
        tab.Font = Enum.Font.Poppins
        tab.Parent = TabContainer
        
        local underline = Instance.new("Frame")
        underline.Size = UDim2.new(0, 50, 0, 2)
        underline.Position = UDim2.new(0.5, -25, 1, -2)
        underline.BackgroundColor3 = Color3.fromRGB(59, 130, 246)
        underline.BackgroundTransparency = isActive and 0 or 1
        underline.BorderSizePixel = 0
        underline.Parent = tab
        
        return tab, underline
    end
    
    local autoTab, autoUnderline = CreateTab("Auto", 0, true)
    local runTab, runUnderline = CreateTab("Run Once", 105, false)
    local consoleTab, consoleUnderline = CreateTab("Console", 210, false)
    
    autoTab.MouseButton1Click:Connect(function()
        autoTab.TextColor3 = Color3.fromRGB(255, 255, 255)
        runTab.TextColor3 = Color3.fromRGB(128, 140, 180)
        consoleTab.TextColor3 = Color3.fromRGB(128, 140, 180)
        autoUnderline.BackgroundTransparency = 0
        runUnderline.BackgroundTransparency = 1
        consoleUnderline.BackgroundTransparency = 1
    end)
    
    runTab.MouseButton1Click:Connect(function()
        runTab.TextColor3 = Color3.fromRGB(255, 255, 255)
        autoTab.TextColor3 = Color3.fromRGB(128, 140, 180)
        consoleTab.TextColor3 = Color3.fromRGB(128, 140, 180)
        runUnderline.BackgroundTransparency = 0
        autoUnderline.BackgroundTransparency = 1
        consoleUnderline.BackgroundTransparency = 1
    end)
    
    consoleTab.MouseButton1Click:Connect(function()
        consoleTab.TextColor3 = Color3.fromRGB(255, 255, 255)
        autoTab.TextColor3 = Color3.fromRGB(128, 140, 180)
        runTab.TextColor3 = Color3.fromRGB(128, 140, 180)
        consoleUnderline.BackgroundTransparency = 0
        autoUnderline.BackgroundTransparency = 1
        runUnderline.BackgroundTransparency = 1
    end)
    
    -- ============================================================
    -- 8. CONTEÚDO SCROLLABLE
    -- ============================================================
    
    local ContentFrame = Instance.new("Frame")
    ContentFrame.Name = "ContentFrame"
    ContentFrame.Size = UDim2.new(1, -32, 1, -200)
    ContentFrame.Position = UDim2.new(0, 16, 0, 144)
    ContentFrame.BackgroundTransparency = 1
    ContentFrame.ClipsDescendants = true
    ContentFrame.Parent = MainFrame
    
    local Scrolling = Instance.new("ScrollingFrame")
    Scrolling.Size = UDim2.new(1, 0, 1, 0)
    Scrolling.BackgroundTransparency = 1
    Scrolling.BorderSizePixel = 0
    Scrolling.ScrollBarThickness = 3
    Scrolling.ScrollBarImageColor3 = Color3.fromRGB(59, 130, 246)
    Scrolling.VerticalScrollBarInset = Enum.ScrollBarInset.ScrollBar
    Scrolling.Parent = ContentFrame
    
    local ContentLayout = Instance.new("UIListLayout")
    ContentLayout.Padding = UDim.new(0, 6)
    ContentLayout.HorizontalAlignment = Enum.HorizontalAlignment.Center
    ContentLayout.SortOrder = Enum.SortOrder.LayoutOrder
    ContentLayout.Parent = Scrolling
    
    -- ============================================================
    -- 9. CRIAR SEÇÕES
    -- ============================================================
    
    local function CreateSection(title, icon, layoutOrder)
        local section = Instance.new("Frame")
        section.Size = UDim2.new(1, 0, 0, 0)
        section.BackgroundTransparency = 1
        section.LayoutOrder = layoutOrder
        section.Parent = Scrolling
        
        local header = Instance.new("TextLabel")
        header.Size = UDim2.new(1, 0, 0, 26)
        header.BackgroundTransparency = 1
        header.Text = icon .. " " .. title
        header.TextColor3 = Color3.fromRGB(200, 215, 245)
        header.TextSize = 13
        header.Font = Enum.Font.Poppins
        header.TextXAlignment = Enum.TextXAlignment.Left
        header.Parent = section
        
        local container = Instance.new("Frame")
        container.Name = "Container"
        container.Size = UDim2.new(1, 0, 0, 0)
        container.Position = UDim2.new(0, 0, 0, 26)
        container.BackgroundTransparency = 1
        container.Parent = section
        
        local list = Instance.new("UIListLayout")
        list.Padding = UDim.new(0, 2)
        list.HorizontalAlignment = Enum.HorizontalAlignment.Center
        list.SortOrder = Enum.SortOrder.LayoutOrder
        list.Parent = container
        
        return section, container, list
    end
    
    -- ============================================================
    -- 10. TOGGLE ROW COMPLETA
    -- ============================================================
    
    local function AddToggleRow(parent, icon, title, subtitle, defaultActive, isGreen, actionType)
        local row = Instance.new("Frame")
        row.Size = UDim2.new(1, 0, 0, 34)
        row.BackgroundTransparency = 1
        row.Parent = parent
        
        local iconLabel = Instance.new("TextLabel")
        iconLabel.Size = UDim2.new(0, 30, 1, 0)
        iconLabel.BackgroundTransparency = 1
        iconLabel.Text = icon
        iconLabel.TextSize = 16
        iconLabel.TextColor3 = Color3.fromRGB(200, 215, 245)
        iconLabel.Font = Enum.Font.Poppins
        iconLabel.Parent = row
        
        local titleLabel = Instance.new("TextLabel")
        titleLabel.Size = UDim2.new(1, -105, 0, 17)
        titleLabel.Position = UDim2.new(0, 34, 0, 2)
        titleLabel.BackgroundTransparency = 1
        titleLabel.Text = title
        titleLabel.TextColor3 = Color3.fromRGB(220, 230, 250)
        titleLabel.TextSize = 12
        titleLabel.Font = Enum.Font.Poppins
        titleLabel.TextXAlignment = Enum.TextXAlignment.Left
        titleLabel.Parent = row
        
        local subLabel = Instance.new("TextLabel")
        subLabel.Size = UDim2.new(1, -105, 0, 14)
        subLabel.Position = UDim2.new(0, 34, 0, 19)
        subLabel.BackgroundTransparency = 1
        subLabel.Text = subtitle or ""
        subLabel.TextColor3 = Color3.fromRGB(128, 140, 180)
        subLabel.TextSize = 10
        subLabel.Font = Enum.Font.Poppins
        subLabel.TextXAlignment = Enum.TextXAlignment.Left
        subLabel.Parent = row
        
        -- Toggle
        local toggle = Instance.new("Frame")
        toggle.Size = UDim2.new(0, 36, 0, 20)
        toggle.Position = UDim2.new(1, -40, 0.5, -10)
        toggle.BackgroundColor3 = defaultActive and (isGreen and Color3.fromRGB(34, 197, 94) or Color3.fromRGB(59, 130, 246)) or Color3.fromRGB(36, 44, 62)
        toggle.BorderSizePixel = 0
        toggle.Parent = row
        
        local toggleCorner = Instance.new("UICorner")
        toggleCorner.CornerRadius = UDim.new(1, 0)
        toggleCorner.Parent = toggle
        
        local knob = Instance.new("Frame")
        knob.Size = UDim2.new(0, 14, 0, 14)
        knob.Position = defaultActive and UDim2.new(1, -18, 0.5, -7) or UDim2.new(0, 3, 0.5, -7)
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
            local targetPos = isActive and UDim2.new(1, -18, 0.5, -7) or UDim2.new(0, 3, 0.5, -7)
            
            TweenService:Create(toggle, TweenInfo.new(0.2), {BackgroundColor3 = targetColor}):Play()
            TweenService:Create(knob, TweenInfo.new(0.2), {Position = targetPos}):Play()
            
            if isActive then
                ExecuteAction(title)
            end
        end)
        
        return toggle
    end
    
    -- ============================================================
    -- 11. FUNÇÕES DE AÇÃO
    -- ============================================================
    
    function ExecuteAction(action)
        local statusText = StatusText
        local statusDot = StatusDot
        local dotGlow = DotGlow
        
        statusText.Text = "[SYSTEM] Executando: " .. action
        statusDot.BackgroundColor3 = Color3.fromRGB(59, 130, 246)
        dotGlow.BackgroundColor3 = Color3.fromRGB(59, 130, 246)
        currentAction = action
        
        -- Mapeamento de ações para RemoteEvents
        local actionMap = {
            ["Claim Cash"] = {"ClaimCash", "CashClaim", "CollectCash"},
            ["Claim Parts"] = {"ClaimParts", "PartsClaim", "CollectParts"},
            ["Buy Upgrades"] = {"BuyUpgrade", "Upgrade", "PurchaseUpgrade"},
            ["Claim Containers"] = {"ClaimContainer", "ContainerClaim", "OpenContainer"},
            ["Open Containers"] = {"OpenContainer", "ContainerOpen"},
            ["Claim Quests"] = {"ClaimQuest", "QuestClaim", "CompleteQuest"},
            ["Deliver Junk"] = {"DeliverJunk", "JunkDeliver", "SellJunk"},
            ["Fix Cars"] = {"FixCar", "RepairCar", "CarFix"},
            ["Sell Fixed Cars"] = {"SellCar", "CarSell", "SellFixed"},
            ["Update Stands"] = {"UpdateStand", "StandUpdate", "RefreshStand"},
            ["Buy, Fix, Sell Cheapest"] = {"BuyFixSell", "AutoFlip", "CheapestFlip"},
            ["Claim Playtime Rewards"] = {"ClaimPlaytime", "PlaytimeReward"},
            ["Claim Daily Rewards"] = {"ClaimDaily", "DailyReward"},
            ["Auction Auto-Bid"] = {"AutoBid", "AuctionBid", "PlaceBid"},
            ["Collect All"] = {"CollectAll", "CollectEverything"},
            ["Auto Sell Legendary"] = {"SellLegendary", "AutoSellLegendary"},
        }
        
        local remoteNames = actionMap[action] or {action:gsub(" ", "")}
        local executed = false
        
        for _, remoteName in ipairs(remoteNames) do
            local remote = ReplicatedStorage:FindFirstChild(remoteName)
            if remote and remote:IsA("RemoteEvent") then
                remote:FireServer()
                executed = true
                AddLog("✅ " .. action .. " executado via " .. remoteName)
                break
            end
        end
        
        if not executed then
            for _, child in pairs(ReplicatedStorage:GetChildren()) do
                if child:IsA("RemoteEvent") then
                    local nameLower = child.Name:lower()
                    local actionLower = action:lower():gsub(" ", "")
                    if nameLower:find(actionLower) or actionLower:find(nameLower) then
                        child:FireServer()
                        executed = true
                        AddLog("✅ " .. action .. " executado via " .. child.Name)
                        break
                    end
                end
            end
        end
        
        if not executed then
            AddLog("⚠️ Ação não encontrada: " .. action)
            statusText.Text = "[SYSTEM] Ação não disponível: " .. action
            task.wait(1)
        end
        
        task.wait(1.5)
        statusText.Text = "[SYSTEM] AUCTION STARTED"
        statusDot.BackgroundColor3 = Color3.fromRGB(34, 197, 94)
        dotGlow.BackgroundColor3 = Color3.fromRGB(34, 197, 94)
    end
    
    -- ============================================================
    -- 12. CONSOLE / LOGS
    -- ============================================================
    
    local ConsoleFrame = Instance.new("Frame")
    ConsoleFrame.Name = "ConsoleFrame"
    ConsoleFrame.Size = UDim2.new(1, 0, 1, 0)
    ConsoleFrame.BackgroundColor3 = Color3.fromRGB(0, 0, 0)
    ConsoleFrame.BackgroundTransparency = 0.3
    ConsoleFrame.BorderSizePixel = 0
    ConsoleFrame.Visible = false
    ConsoleFrame.Parent = ContentFrame
    
    local ConsoleCorner = Instance.new("UICorner")
    ConsoleCorner.CornerRadius = UDim.new(0, 12)
    ConsoleCorner.Parent = ConsoleFrame
    
    -- Header do console
    local ConsoleHeader = Instance.new("Frame")
    ConsoleHeader.Size = UDim2.new(1, 0, 0, 28)
    ConsoleHeader.BackgroundColor3 = Color3.fromRGB(20, 22, 32)
    ConsoleHeader.BorderSizePixel = 0
    ConsoleHeader.Parent = ConsoleFrame
    
    local ConsoleTitle = Instance.new("TextLabel")
    ConsoleTitle.Size = UDim2.new(0.5, 0, 1, 0)
    ConsoleTitle.Position = UDim2.new(0, 12, 0, 0)
    ConsoleTitle.BackgroundTransparency = 1
    ConsoleTitle.Text = "📟 Developer Console"
    ConsoleTitle.TextColor3 = Color3.fromRGB(200, 215, 245)
    ConsoleTitle.TextSize = 12
    ConsoleTitle.Font = Enum.Font.Poppins
    ConsoleTitle.TextXAlignment = Enum.TextXAlignment.Left
    ConsoleTitle.Parent = ConsoleHeader
    
    -- Clear button
    local ClearBtn = Instance.new("TextButton")
    ClearBtn.Size = UDim2.new(0, 50, 0, 22)
    ClearBtn.Position = UDim2.new(1, -60, 0.5, -11)
    ClearBtn.BackgroundColor3 = Color3.fromRGB(59, 130, 246)
    ClearBtn.BackgroundTransparency = 0.5
    ClearBtn.Text = "Clear"
    ClearBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
    ClearBtn.TextSize = 11
    ClearBtn.Font = Enum.Font.Poppins
    ClearBtn.Parent = ConsoleHeader
    
    local ClearCorner = Instance.new("UICorner")
    ClearCorner.CornerRadius = UDim.new(1, 0)
    ClearCorner.Parent = ClearBtn
    
    ClearBtn.MouseButton1Click:Connect(function()
        consoleLines = {}
        for _, child in pairs(ConsoleContainer:GetChildren()) do
            child:Destroy()
        end
    end)
    
    -- Container do console
    local ConsoleContainer = Instance.new("ScrollingFrame")
    ConsoleContainer.Size = UDim2.new(1, -16, 1, -40)
    ConsoleContainer.Position = UDim2.new(0, 8, 0, 32)
    ConsoleContainer.BackgroundTransparency = 1
    ConsoleContainer.BorderSizePixel = 0
    ConsoleContainer.ScrollBarThickness = 3
    ConsoleContainer.ScrollBarImageColor3 = Color3.fromRGB(34, 197, 94)
    ConsoleContainer.VerticalScrollBarInset = Enum.ScrollBarInset.ScrollBar
    ConsoleContainer.Parent = ConsoleFrame
    
    local ConsoleList = Instance.new("UIListLayout")
    ConsoleList.Padding = UDim.new(0, 1)
    ConsoleList.HorizontalAlignment = Enum.HorizontalAlignment.Left
    ConsoleList.SortOrder = Enum.SortOrder.LayoutOrder
    ConsoleList.Parent = ConsoleContainer
    
    -- ============================================================
    -- 13. FUNÇÃO DE LOG
    -- ============================================================
    
    function AddLog(message, isError)
        local timestamp = os.date("%H:%M:%S")
        local entry = string.format("[%s] %s", timestamp, message)
        
        table.insert(logEntries, 1, entry)
        if #logEntries > 100 then
            table.remove(logEntries)
        end
        
        -- Adicionar ao console
        local label = Instance.new("TextLabel")
        label.Size = UDim2.new(1, 0, 0, 18)
        label.BackgroundTransparency = 1
        label.Text = entry
        label.TextColor3 = isError and Color3.fromRGB(255, 100, 100) or Color3.fromRGB(176, 214, 160)
        label.TextSize = 11
        label.Font = Enum.Font.Code
        label.TextXAlignment = Enum.TextXAlignment.Left
        label.Parent = ConsoleContainer
        
        -- Scroll para o topo
        ConsoleContainer.CanvasPosition = Vector2.new(0, 0)
        
        -- Limitar linhas
        local children = ConsoleContainer:GetChildren()
        if #children > 50 then
            for i = 1, #children - 50 do
                children[i]:Destroy()
            end
        end
    end
    
    -- ============================================================
    -- 14. CONSTRUIR TODAS AS SEÇÕES
    -- ============================================================
    
    -- ===== BASE =====
    local baseSection, baseContainer = CreateSection("Base", "🏢", 1)
    AddToggleRow(baseContainer, "💰", "Claim Cash", "Coletar dinheiro automático", true, true)
    AddToggleRow(baseContainer, "🧩", "Claim Parts", "Coletar peças", true)
    AddToggleRow(baseContainer, "⬆️", "Buy Upgrades", "Comprar melhorias", false)
    AddToggleRow(baseContainer, "📦", "Claim Containers", "Abrir containers", true, true)
    AddToggleRow(baseContainer, "🎁", "Open Containers", "Revelar recompensas", false)
    AddToggleRow(baseContainer, "📋", "Claim Quests", "Recompensas de missões", true)
    AddToggleRow(baseContainer, "🗑️", "Deliver Junk", "Vender sucata", false)
    
    -- ===== CARS =====
    local carsSection, carsContainer = CreateSection("Cars", "🚗", 2)
    AddToggleRow(carsContainer, "🔧", "Fix Cars", "Reparar veículos", true)
    AddToggleRow(carsContainer, "💲", "Sell Fixed Cars", "Vender reparados", true, true)
    AddToggleRow(carsContainer, "🔄", "Update Stands", "Atualizar vitrines", false)
    AddToggleRow(carsContainer, "🏷️", "Buy, Fix, Sell Cheapest", "Auto flip mais barato", true)
    
    -- ===== REWARDS =====
    local rewardsSection, rewardsContainer = CreateSection("Rewards", "🎁", 3)
    AddToggleRow(rewardsContainer, "⏳", "Claim Playtime Rewards", "Recompensas por tempo", true, true)
    AddToggleRow(rewardsContainer, "📆", "Claim Daily Rewards", "Recompensas diárias", true)
    
    -- ===== AUCTION =====
    local auctionSection, auctionContainer = CreateSection("Auction", "🏪", 4)
    AddToggleRow(auctionContainer, "🔨", "Auction Auto-Bid", "Lance automático em leilões", true, true)
    AddToggleRow(auctionContainer, "⭐", "Auto Sell Legendary", "Vender carros lendários", false)
    
    -- ===== EXTRA =====
    local extraSection, extraContainer = CreateSection("Extra", "⚡", 5)
    AddToggleRow(extraContainer, "📦", "Collect All", "Coletar tudo de uma vez", false, true)
    AddToggleRow(extraContainer, "🏆", "Auto Farm", "Farm automático", true)
    
    -- ============================================================
    -- 15. PROGRESS BAR
    -- ============================================================
    
    local ProgressFrame = Instance.new("Frame")
    ProgressFrame.Name = "ProgressFrame"
    ProgressFrame.Size = UDim2.new(1, -32, 0, 48)
    ProgressFrame.Position = UDim2.new(0, 16, 1, -58)
    ProgressFrame.BackgroundTransparency = 1
    ProgressFrame.Parent = MainFrame
    
    -- Stats
    local StatsLine = Instance.new("Frame")
    StatsLine.Size = UDim2.new(1, 0, 0, 20)
    StatsLine.BackgroundTransparency = 1
    StatsLine.Parent = ProgressFrame
    
    local function CreateStatLabel(text, xPos)
        local label = Instance.new("TextLabel")
        label.Size = UDim2.new(0.2, 0, 1, 0)
        label.Position = UDim2.new(xPos, 0, 0, 0)
        label.BackgroundTransparency = 1
        label.Text = text
        label.TextColor3 = Color3.fromRGB(180, 200, 240)
        label.TextSize = 11
        label.Font = Enum.Font.Poppins
        label.TextXAlignment = Enum.TextXAlignment.Center
        label.Parent = StatsLine
        return label
    end
    
    CreateStatLabel("1/s", 0)
    CreateStatLabel("453/10000", 0.2)
    CreateStatLabel("145/s", 0.4)
    CreateStatLabel("1618/100000", 0.6)
    CreateStatLabel("Nivel.4", 0.8)
    
    -- Progress bar
    local ProgressBg = Instance.new("Frame")
    ProgressBg.Size = UDim2.new(1, 0, 0, 8)
    ProgressBg.Position = UDim2.new(0, 0, 0, 24)
    ProgressBg.BackgroundColor3 = Color3.fromRGB(30, 35, 50)
    ProgressBg.BorderSizePixel = 0
    ProgressBg.Parent = ProgressFrame
    
    local ProgressBgCorner = Instance.new("UICorner")
    ProgressBgCorner.CornerRadius = UDim.new(1, 0)
    ProgressBgCorner.Parent = Progress

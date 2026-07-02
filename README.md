--[[
    Car Flipper - Painel Completo com Correção de Erros
    Versão 8.0 - Resolve "attempt to call a nil value"
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
local CoreGui = game:GetService("CoreGui")

repeat task.wait() until Player and Player.Character and Player.Character:FindFirstChild("Humanoid")
print("🚗 Car Flipper Painel Completo Iniciando...")

-- ============================================================
-- 2. FIX: CORREÇÃO DO ERRO "attempt to call a nil value"
-- ============================================================

-- Esta função substitui chamadas nil com segurança
local function SafeCall(func, ...)
    local success, result = pcall(func, ...)
    if not success then
        warn("⚠️ Erro capturado: " .. tostring(result))
        AddLog("⚠️ Erro: " .. tostring(result), "Error")
        return nil
    end
    return result
end

-- Sobrescreve funções problemáticas
local originalFireServer = nil

-- Patch seguro para RemoteEvents
local function SafeFireServer(remote, ...)
    if not remote then 
        AddLog("⚠️ RemoteEvent não encontrado", "Warning")
        return false 
    end
    if not remote:IsA("RemoteEvent") then 
        AddLog("⚠️ Objeto não é um RemoteEvent", "Warning")
        return false 
    end
    
    local success, result = pcall(function()
        remote:FireServer(...)
    end)
    
    if not success then
        AddLog("❌ Erro ao executar: " .. tostring(result), "Error")
        return false
    end
    return true
end

-- ============================================================
-- 3. VARIÁVEIS GLOBAIS
-- ============================================================

local isRunning = false
local autoFarmActive = false
local currentAction = "Idle"
local logEntries = {}
local consoleLines = {}
local consoleMode = "Log"

-- ============================================================
-- 4. CRIAR UI PRINCIPAL
-- ============================================================

local function CreateCarFlipperUI()
    local ScreenGui = Instance.new("ScreenGui")
    ScreenGui.Name = "CarFlipperPanel"
    ScreenGui.ResetOnSpawn = false
    ScreenGui.ZIndexBehavior = Enum.ZIndexBehavior.Sibling
    ScreenGui.Parent = Player.PlayerGui
    
    -- ============================================================
    -- 5. JANELA PRINCIPAL
    -- ============================================================
    
    local MainFrame = Instance.new("Frame")
    MainFrame.Name = "MainFrame"
    MainFrame.Size = UDim2.new(0, 540, 0, 700)
    MainFrame.Position = UDim2.new(0.5, -270, 0.5, -350)
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
    -- 6. HEADER COM INFO DO JOGO
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
    Title.Size = UDim2.new(0.35, 0, 0.5, 0)
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
    MoneyLabel.Size = UDim2.new(0.35, 0, 0.5, 0)
    MoneyLabel.Position = UDim2.new(0.35, 0, 0, 4)
    MoneyLabel.BackgroundTransparency = 1
    MoneyLabel.Text = "💰 1,522,964$"
    MoneyLabel.TextColor3 = Color3.fromRGB(255, 215, 0)
    MoneyLabel.TextSize = 16
    MoneyLabel.Font = Enum.Font.Poppins
    MoneyLabel.TextXAlignment = Enum.TextXAlignment.Right
    MoneyLabel.Parent = Header
    
    -- Memory Usage
    local MemoryLabel = Instance.new("TextLabel")
    MemoryLabel.Size = UDim2.new(0.25, 0, 0.5, 0)
    MemoryLabel.Position = UDim2.new(0.7, 0, 0, 4)
    MemoryLabel.BackgroundTransparency = 1
    MemoryLabel.Text = "📊 648 MB"
    MemoryLabel.TextColor3 = Color3.fromRGB(100, 200, 255)
    MemoryLabel.TextSize = 12
    MemoryLabel.Font = Enum.Font.Poppins
    MemoryLabel.TextXAlignment = Enum.TextXAlignment.Right
    MemoryLabel.Parent = Header
    
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
    
    -- Player
    local PlayerLabel = Instance.new("TextLabel")
    PlayerLabel.Size = UDim2.new(0.4, 0, 0.4, 0)
    PlayerLabel.Position = UDim2.new(0.6, 0, 0, 32)
    PlayerLabel.BackgroundTransparency = 1
    PlayerLabel.Text = "👤 " .. Player.Name
    PlayerLabel.TextColor3 = Color3.fromRGB(180, 200, 240)
    PlayerLabel.TextSize = 11
    PlayerLabel.Font = Enum.Font.Poppins
    PlayerLabel.TextXAlignment = Enum.TextXAlignment.Right
    PlayerLabel.Parent = Header
    
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
    -- 7. STATUS BAR
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
    StatusText.Size = UDim2.new(0.6, 0, 1, 0)
    StatusText.Position = UDim2.new(0, 28, 0, 0)
    StatusText.BackgroundTransparency = 1
    StatusText.Text = "[SYSTEM] AUCTION STARTED"
    StatusText.TextColor3 = Color3.fromRGB(180, 200, 240)
    StatusText.TextSize = 11
    StatusText.Font = Enum.Font.Poppins
    StatusText.TextXAlignment = Enum.TextXAlignment.Left
    StatusText.Parent = StatusBar
    
    -- Info adicional
    local ExtraInfo = Instance.new("TextLabel")
    ExtraInfo.Name = "ExtraInfo"
    ExtraInfo.Size = UDim2.new(0.3, 0, 1, 0)
    ExtraInfo.Position = UDim2.new(0.7, 0, 0, 0)
    ExtraInfo.BackgroundTransparency = 1
    ExtraInfo.Text = "1/3.0"
    ExtraInfo.TextColor3 = Color3.fromRGB(128, 140, 180)
    ExtraInfo.TextSize = 11
    ExtraInfo.Font = Enum.Font.Poppins
    ExtraInfo.TextXAlignment = Enum.TextXAlignment.Right
    ExtraInfo.Parent = StatusBar
    
    -- ============================================================
    -- 8. TABS
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
        ShowContent("Auto")
    end)
    
    runTab.MouseButton1Click:Connect(function()
        runTab.TextColor3 = Color3.fromRGB(255, 255, 255)
        autoTab.TextColor3 = Color3.fromRGB(128, 140, 180)
        consoleTab.TextColor3 = Color3.fromRGB(128, 140, 180)
        runUnderline.BackgroundTransparency = 0
        autoUnderline.BackgroundTransparency = 1
        consoleUnderline.BackgroundTransparency = 1
        ShowContent("RunOnce")
    end)
    
    consoleTab.MouseButton1Click:Connect(function()
        consoleTab.TextColor3 = Color3.fromRGB(255, 255, 255)
        autoTab.TextColor3 = Color3.fromRGB(128, 140, 180)
        runTab.TextColor3 = Color3.fromRGB(128, 140, 180)
        consoleUnderline.BackgroundTransparency = 0
        autoUnderline.BackgroundTransparency = 1
        runUnderline.BackgroundTransparency = 1
        ShowContent("Console")
    end)
    
    -- ============================================================
    -- 9. CONTEÚDO
    -- ============================================================
    
    local ContentFrame = Instance.new("Frame")
    ContentFrame.Name = "ContentFrame"
    ContentFrame.Size = UDim2.new(1, -32, 1, -200)
    ContentFrame.Position = UDim2.new(0, 16, 0, 144)
    ContentFrame.BackgroundTransparency = 1
    ContentFrame.ClipsDescendants = true
    ContentFrame.Parent = MainFrame
    
    -- Scrolling para Auto/RunOnce
    local Scrolling = Instance.new("ScrollingFrame")
    Scrolling.Name = "Scrolling"
    Scrolling.Size = UDim2.new(1, 0, 1, 0)
    Scrolling.BackgroundTransparency = 1
    Scrolling.BorderSizePixel = 0
    Scrolling.ScrollBarThickness = 3
    Scrolling.ScrollBarImageColor3 = Color3.fromRGB(59, 130, 246)
    Scrolling.VerticalScrollBarInset = Enum.ScrollBarInset.ScrollBar
    Scrolling.Visible = true
    Scrolling.Parent = ContentFrame
    
    local ContentLayout = Instance.new("UIListLayout")
    ContentLayout.Padding = UDim.new(0, 6)
    ContentLayout.HorizontalAlignment = Enum.HorizontalAlignment.Center
    ContentLayout.SortOrder = Enum.SortOrder.LayoutOrder
    ContentLayout.Parent = Scrolling
    
    -- ============================================================
    -- 10. CONSOLE COMPLETO
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
    ConsoleHeader.Size = UDim2.new(1, 0, 0, 34)
    ConsoleHeader.BackgroundColor3 = Color3.fromRGB(20, 22, 32)
    ConsoleHeader.BorderSizePixel = 0
    ConsoleHeader.Parent = ConsoleFrame
    
    local ConsoleTitle = Instance.new("TextLabel")
    ConsoleTitle.Size = UDim2.new(0.2, 0, 1, 0)
    ConsoleTitle.Position = UDim2.new(0, 12, 0, 0)
    ConsoleTitle.BackgroundTransparency = 1
    ConsoleTitle.Text = "📟 Console"
    ConsoleTitle.TextColor3 = Color3.fromRGB(200, 215, 245)
    ConsoleTitle.TextSize = 12
    ConsoleTitle.Font = Enum.Font.Poppins
    ConsoleTitle.TextXAlignment = Enum.TextXAlignment.Left
    ConsoleTitle.Parent = ConsoleHeader
    
    -- Abas do console
    local ConsoleTabs = Instance.new("Frame")
    ConsoleTabs.Size = UDim2.new(0.6, 0, 1, 0)
    ConsoleTabs.Position = UDim2.new(0.25, 0, 0, 0)
    ConsoleTabs.BackgroundTransparency = 1
    ConsoleTabs.Parent = ConsoleHeader
    
    local consoleTabNames = {"Log", "Information", "Warning", "Error", "Memory"}
    local consoleTabButtons = {}
    local consoleTabUnderlines = {}
    
    for i, name in ipairs(consoleTabNames) do
        local tab = Instance.new("TextButton")
        tab.Size = UDim2.new(0, 55, 1, 0)
        tab.Position = UDim2.new(0, (i-1) * 60, 0, 0)
        tab.BackgroundTransparency = 1
        tab.Text = name
        tab.TextColor3 = i == 1 and Color3.fromRGB(255, 255, 255) or Color3.fromRGB(128, 140, 180)
        tab.TextSize = 10
        tab.Font = Enum.Font.Poppins
        tab.Parent = ConsoleTabs
        
        local underline = Instance.new("Frame")
        underline.Size = UDim2.new(0, 35, 0, 2)
        underline.Position = UDim2.new(0.5, -17, 1, -2)
        underline.BackgroundColor3 = Color3.fromRGB(59, 130, 246)
        underline.BackgroundTransparency = i == 1 and 0 or 1
        underline.BorderSizePixel = 0
        underline.Parent = tab
        
        consoleTabButtons[i] = tab
        consoleTabUnderlines[i] = underline
        
        tab.MouseButton1Click:Connect(function()
            for j, btn in ipairs(consoleTabButtons) do
                btn.TextColor3 = Color3.fromRGB(128, 140, 180)
                consoleTabUnderlines[j].BackgroundTransparency = 1
            end
            tab.TextColor3 = Color3.fromRGB(255, 255, 255)
            underline.BackgroundTransparency = 0
            consoleMode = name
            UpdateConsole()
        end)
    end
    
    -- Clear button
    local ClearBtn = Instance.new("TextButton")
    ClearBtn.Size = UDim2.new(0, 50, 0, 24)
    ClearBtn.Position = UDim2.new(1, -60, 0.5, -12)
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
        UpdateConsole()
    end)
    
    -- Container do console
    local ConsoleContainer = Instance.new("ScrollingFrame")
    ConsoleContainer.Size = UDim2.new(1, -16, 1, -46)
    ConsoleContainer.Position = UDim2.new(0, 8, 0, 38)
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
    -- 11. FUNÇÃO DE LOG
    -- ============================================================
    
    function AddLog(message, logType)
        logType = logType or "Log"
        local timestamp = os.date("%H:%M:%S")
        local entry = {
            text = string.format("[%s] %s", timestamp, message),
            type = logType,
            time = timestamp
        }
        
        table.insert(consoleLines, 1, entry)
        if #consoleLines > 200 then
            table.remove(consoleLines)
        end
        
        if ConsoleFrame.Visible then
            UpdateConsole()
        end
    end
    
    function UpdateConsole()
        for _, child in pairs(ConsoleContainer:GetChildren()) do
            if child:IsA("TextLabel") then
                child:Destroy()
            end
        end
        
        local colorMap = {
            Log = Color3.fromRGB(176, 214, 160),
            Information = Color3.fromRGB(100, 180, 255),
            Warning = Color3.fromRGB(255, 200, 100),
            Error = Color3.fromRGB(255, 100, 100),
            Memory = Color3.fromRGB(200, 150, 255)
        }
        
        local displayColor = colorMap[consoleMode] or Color3.fromRGB(176, 214, 160)
        
        for _, entry in ipairs(consoleLines) do
            if entry.type == consoleMode or consoleMode == "Log" then
                local label = Instance.new("TextLabel")
                label.Size = UDim2.new(1, 0, 0, 18)
                label.BackgroundTransparency = 1
                label.Text = entry.text
                label.TextColor3 = entry.type == consoleMode and displayColor or Color3.fromRGB(128, 140, 180)
                label.TextSize = 11
                label.Font = Enum.Font.Code
                label.TextXAlignment = Enum.TextXAlignment.Left
                label.Parent = ConsoleContainer
            end
        end
        
        ConsoleContainer.CanvasPosition = Vector2.new(0, 0)
    end
    
    -- ============================================================
    -- 12. CRIAR SEÇÕES
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
    -- 13. TOGGLE ROW
    -- ============================================================
    
    local function AddToggleRow(parent, icon, title, subtitle, defaultActive, isGreen)
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
    -- 14. FUNÇÕES DE AÇÃO (COM SEGURANÇA)
    -- ============================================================
    
    function ExecuteAction(action)
        StatusText.Text = "[SYSTEM] Executando: " .. action
        StatusDot.BackgroundColor3 = Color3.fromRGB(59, 130, 246)
        DotGlow.BackgroundColor3 = Color3.fromRGB(59, 130, 246)
        currentAction = action
        
        -- Mapeamento de ações com nomes alternativos
        local actionMap = {
            ["Claim Cash"] = {"ClaimCash", "CashClaim", "CollectCash", "Claim_Money"},
            ["Claim Parts"] = {"ClaimParts", "PartsClaim", "CollectParts", "Claim_Parts"},
            ["Buy Upgrades"] = {"BuyUpgrade", "Upgrade", "PurchaseUpgrade", "Buy_Upgrade"},
            ["Claim Containers"] = {"ClaimContainer", "ContainerClaim", "OpenContainer", "Claim_Container"},
            ["Open Containers"] = {"OpenContainer", "ContainerOpen", "Open_Container"},
            ["Claim Quests"] = {"ClaimQuest", "QuestClaim", "CompleteQuest", "Claim_Quest"},
            ["Deliver Junk"] = {"DeliverJunk", "JunkDeliver", "SellJunk", "Deliver_Junk"},
            ["Fix Cars"] = {"FixCar", "RepairCar", "CarFix", "Fix_Car"},
            ["Sell Fixed Cars"] = {"SellCar", "CarSell", "SellFixed", "Sell_Car"},
            ["Update Stands"] = {"UpdateStand", "StandUpdate", "RefreshStand", "Update_Stand"},
            ["Buy, Fix, Sell Cheapest"] = {"BuyFixSell", "AutoFlip", "CheapestFlip", "Auto_Sell"},
            ["Claim Playtime Rewards"] = {"ClaimPlaytime", "PlaytimeReward", "Claim_Playtime"},
            ["Claim Daily Rewards"] = {"ClaimDaily", "DailyReward", "Claim_Daily"},
            ["Auction Auto-Bid"] = {"AutoBid", "AuctionBid", "PlaceBid", "Auto_Bid"},
            ["Collect All"] = {"CollectAll", "CollectEverything", "Collect_All"},
            ["Auto Sell Legendary"] = {"SellLegendary", "AutoSellLegendary", "Sell_Legendary"},
            ["SWIZ acquired"] = {"SWIZ", "AcquireSWIZ", "SWIZ_Acquire"},
            ["Common container"] = {"CommonContainer", "Container_Common"},
            ["Upgrading your base"] = {"UpgradeBase", "BaseUpgrade", "Upgrade_Base"},
        }
        
        local remoteNames = actionMap[action] or {action:gsub(" ", ""), action}
        local executed = false
        
        -- Primeiro tenta encontrar RemoteEvents no ReplicatedStorage
        for _, remoteName in ipairs(remoteNames) do
            local remote = ReplicatedStorage:FindFirstChild(remoteName)
            if remote then
                if remote:IsA("RemoteEvent") then
                    local success = SafeFireServer(remote)
                    if success then
                        executed = true
                        AddLog("✅ " .. action .. " executado via " .. remoteName, "Information")
                        break
                    end
                elseif remote:IsA("BindableEvent") then
                    -- Tentar BindableEvents também
                    local success, result = pcall(function()
                        remote:Fire()
                    end)
                    if success then
                        executed = true
                        AddLog("✅ " .. action .. " executado via " .. remoteName, "Information")
                        break
                    end
                end
            end
        end
        
        -- Se não encontrou, tentar procurar em

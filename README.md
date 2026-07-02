--[[
    Car Flipper - Interface Premium
    Design inspirado nas imagens de referência
    Estilo minimalista com tema escuro e verde/azul
]]

-- ============================================
-- SERVIÇOS
-- ============================================
local Players = game:GetService("Players")
local UserInputService = game:GetService("UserInputService")
local TweenService = game:GetService("TweenService")
local RunService = game:GetService("RunService")
local LocalPlayer = Players.LocalPlayer
local Mouse = LocalPlayer:GetMouse()

-- ============================================
-- CONFIGURAÇÕES
-- ============================================
local Config = {
    Theme = {
        Background = Color3.fromHex("0A0A0A"),
        BackgroundSecondary = Color3.fromHex("0F0F0F"),
        BackgroundTertiary = Color3.fromHex("141414"),
        Primary = Color3.fromHex("00FF88"),
        PrimaryDark = Color3.fromHex("00CC66"),
        PrimaryLight = Color3.fromHex("33FF99"),
        Text = Color3.fromHex("FFFFFF"),
        TextSecondary = Color3.fromHex("88FFBB"),
        TextMuted = Color3.fromHex("445544"),
        Border = Color3.fromHex("1A2A1A"),
        Success = Color3.fromHex("00FF88"),
        Danger = Color3.fromHex("FF3366"),
        Warning = Color3.fromHex("FFAA00"),
        Hover = Color3.fromHex("1A2A1A"),
    },
    Font = Enum.Font.Gotham,
    FontBold = Enum.Font.GothamBold,
    BlurEnabled = true,
    SoundEnabled = true,
    WindowTitle = "Car Flipper - Alexandria",
}

-- ============================================
-- VARIÁVEIS GLOBAIS
-- ============================================
local MainFrame
local StatusLabel
local ConsoleOutput
local UIBlur
local Dragging = false
local DragStart = Vector2.new()
local DragStartPosition = UDim2.new()
local Logs = {}
local CurrentTab = "Auto"
local Toggles = {}

-- ============================================
-- FUNÇÕES AUXILIARES
-- ============================================
local function CreateTween(object, properties, duration, style)
    style = style or Enum.EasingStyle.Quad
    local tweenInfo = TweenInfo.new(duration or 0.3, style)
    local tween = TweenService:Create(object, tweenInfo, properties)
    return tween
end

local function PlaySound()
    if not Config.SoundEnabled then return end
    local sound = Instance.new("Sound")
    sound.SoundId = "rbxassetid://9120386751"
    sound.Volume = 0.15
    sound.Parent = MainFrame or game:GetService("CoreGui")
    sound:Play()
    game:GetService("Debris"):AddItem(sound, 1)
end

local function AddLog(text, color)
    color = color or Config.Theme.TextSecondary
    table.insert(Logs, {Text = text, Color = color})
    if #Logs > 100 then
        table.remove(Logs, 1)
    end
    UpdateConsole()
end

local function UpdateConsole()
    if not ConsoleOutput then return end
    local text = ""
    for i = math.max(1, #Logs - 30), #Logs do
        local log = Logs[i]
        if log then
            text = text .. log.Text .. "\n"
        end
    end
    ConsoleOutput.Text = text
end

-- ============================================
-- CRIAÇÃO DA INTERFACE
-- ============================================
local function CreateUI()
    local ScreenGui = Instance.new("ScreenGui")
    ScreenGui.Name = "CarFlipperUI"
    ScreenGui.Parent = LocalPlayer:WaitForChild("PlayerGui")
    ScreenGui.ResetOnSpawn = false
    ScreenGui.ZIndexBehavior = Enum.ZIndexBehavior.Sibling

    -- Blur
    if Config.BlurEnabled then
        UIBlur = Instance.new("BlurEffect")
        UIBlur.Size = 0
        UIBlur.Parent = game:GetService("Lighting")
    end

    -- Main Frame
    MainFrame = Instance.new("Frame")
    MainFrame.Name = "MainFrame"
    MainFrame.Size = UDim2.fromOffset(380, 500)
    MainFrame.Position = UDim2.fromScale(0.5, 0.5) - UDim2.fromOffset(190, 250)
    MainFrame.BackgroundColor3 = Config.Theme.Background
    MainFrame.BackgroundTransparency = 0.95
    MainFrame.Parent = ScreenGui
    MainFrame.ClipsDescendants = true

    -- Shadow
    local Shadow = Instance.new("ImageLabel")
    Shadow.Name = "Shadow"
    Shadow.Size = UDim2.fromScale(1, 1)
    Shadow.Position = UDim2.fromOffset(5, 5)
    Shadow.BackgroundColor3 = Color3.fromHex("000000")
    Shadow.BackgroundTransparency = 0.5
    Shadow.Image = "rbxassetid://1313878513"
    Shadow.ImageColor3 = Color3.fromHex("000000")
    Shadow.ImageTransparency = 0.8
    Shadow.ZIndex = 0
    Shadow.Parent = MainFrame

    -- UI Corner
    local Corner = Instance.new("UICorner")
    Corner.CornerRadius = UDim.new(0, 8)
    Corner.Parent = MainFrame

    -- UI Stroke
    local Stroke = Instance.new("UIStroke")
    Stroke.Color = Config.Theme.Border
    Stroke.Thickness = 1
    Stroke.Parent = MainFrame

    -- ============================================
    -- HEADER
    -- ============================================
    local Header = Instance.new("Frame")
    Header.Name = "Header"
    Header.Size = UDim2.fromScale(1, 0.07)
    Header.BackgroundColor3 = Config.Theme.BackgroundSecondary
    Header.Parent = MainFrame

    local HeaderCorner = Instance.new("UICorner")
    HeaderCorner.CornerRadius = UDim.new(0, 8)
    HeaderCorner.Parent = Header

    -- Título
    local Title = Instance.new("TextLabel")
    Title.Name = "Title"
    Title.Size = UDim2.fromScale(0.8, 1)
    Title.Position = UDim2.fromOffset(12, 0)
    Title.BackgroundTransparency = 1
    Title.Text = Config.WindowTitle
    Title.TextColor3 = Config.Theme.Primary
    Title.TextSize = 14
    Title.TextXAlignment = Enum.TextXAlignment.Left
    Title.Font = Config.FontBold
    Title.Parent = Header

    -- Botão Fechar
    local CloseButton = Instance.new("TextButton")
    CloseButton.Name = "CloseButton"
    CloseButton.Size = UDim2.fromOffset(24, 24)
    CloseButton.Position = UDim2.fromScale(1, 0) - UDim2.fromOffset(30, 4)
    CloseButton.BackgroundColor3 = Config.Theme.Background
    CloseButton.Text = "✕"
    CloseButton.TextColor3 = Config.Theme.TextMuted
    CloseButton.TextSize = 12
    CloseButton.TextScaled = true
    CloseButton.Font = Config.Font
    CloseButton.Parent = Header

    local CloseCorner = Instance.new("UICorner")
    CloseCorner.CornerRadius = UDim.new(0, 4)
    CloseCorner.Parent = CloseButton

    -- ============================================
    -- TABS
    -- ============================================
    local TabBar = Instance.new("Frame")
    TabBar.Name = "TabBar"
    TabBar.Size = UDim2.fromScale(1, 0.055)
    TabBar.Position = UDim2.fromScale(0, 0.07)
    TabBar.BackgroundColor3 = Config.Theme.BackgroundSecondary
    TabBar.Parent = MainFrame

    local TabCorner = Instance.new("UICorner")
    TabCorner.CornerRadius = UDim.new(0, 4)
    TabCorner.Parent = TabBar

    -- Botões das abas
    local Tabs = {"Auto", "Run Once"}
    local TabButtons = {}

    for i, tabName in ipairs(Tabs) do
        local btn = Instance.new("TextButton")
        btn.Name = tabName
        btn.Size = UDim2.fromScale(0.5, 1)
        btn.Position = UDim2.fromScale((i-1) * 0.5, 0)
        btn.BackgroundColor3 = i == 1 and Config.Theme.Primary or Config.Theme.BackgroundSecondary
        btn.Text = tabName
        btn.TextColor3 = i == 1 and Config.Theme.Background or Config.Theme.TextSecondary
        btn.TextSize = 12
        btn.Font = Config.FontBold
        btn.Parent = TabBar

        TabButtons[tabName] = btn
    end

    -- ============================================
    -- STATUS
    -- ============================================
    local StatusBar = Instance.new("Frame")
    StatusBar.Name = "StatusBar"
    StatusBar.Size = UDim2.fromScale(1, 0.035)
    StatusBar.Position = UDim2.fromScale(0, 0.125)
    StatusBar.BackgroundColor3 = Config.Theme.BackgroundSecondary
    StatusBar.Parent = MainFrame

    StatusLabel = Instance.new("TextLabel")
    StatusLabel.Name = "StatusLabel"
    StatusLabel.Size = UDim2.fromScale(1, 1)
    StatusLabel.Position = UDim2.fromOffset(10, 0)
    StatusLabel.BackgroundTransparency = 1
    StatusLabel.Text = "[Car Flipper] Idle"
    StatusLabel.TextColor3 = Config.Theme.TextSecondary
    StatusLabel.TextSize = 10
    StatusLabel.TextXAlignment = Enum.TextXAlignment.Left
    StatusLabel.Font = Config.Font
    StatusLabel.Parent = StatusBar

    -- ============================================
    -- CONTENT (Scrollable)
    -- ============================================
    local ContentFrame = Instance.new("ScrollingFrame")
    ContentFrame.Name = "ContentFrame"
    ContentFrame.Size = UDim2.fromScale(1, 0.62)
    ContentFrame.Position = UDim2.fromScale(0, 0.16)
    ContentFrame.BackgroundColor3 = Config.Theme.Background
    ContentFrame.BackgroundTransparency = 0.5
    ContentFrame.Parent = MainFrame
    ContentFrame.ScrollBarThickness = 2
    ContentFrame.ScrollBarImageColor3 = Config.Theme.Primary
    ContentFrame.ScrollBarImageTransparency = 0.3

    local ContentCorner = Instance.new("UICorner")
    ContentCorner.CornerRadius = UDim.new(0, 4)
    ContentCorner.Parent = ContentFrame

    -- Layout
    local ContentLayout = Instance.new("UIListLayout")
    ContentLayout.Name = "ContentLayout"
    ContentLayout.FillDirection = Enum.FillDirection.Vertical
    ContentLayout.SortOrder = Enum.SortOrder.LayoutOrder
    ContentLayout.Padding = UDim.new(0, 1)
    ContentLayout.Parent = ContentFrame

    -- ============================================
    -- CATEGORIAS
    -- ============================================
    local Categories = {
        {
            Name = "Base",
            Items = {
                "Claim Cash",
                "Claim Parts",
                "Buy Upgrades",
                "Claim Containers",
                "Open Containers",
                "Claim Quests",
                "Deliver Junk"
            }
        },
        {
            Name = "Cars",
            Items = {
                "Fix Cars",
                "Sell Fixed Cars",
                "Update Stands",
                "Buy, Fix, Sell Cheapest"
            }
        },
        {
            Name = "Rewards",
            Items = {
                "Claim Playtime Rewards",
                "Claim Daily Rewards"
            }
        }
    }

    -- ============================================
    -- CRIAÇÃO DOS TOGGLES
    -- ============================================
    local function CreateToggle(itemName, parent)
        local frame = Instance.new("Frame")
        frame.Name = itemName
        frame.Size = UDim2.fromScale(1, 0.065)
        frame.BackgroundColor3 = Config.Theme.BackgroundTertiary
        frame.BackgroundTransparency = 0.3
        frame.Parent = parent

        local frameCorner = Instance.new("UICorner")
        frameCorner.CornerRadius = UDim.new(0, 3)
        frameCorner.Parent = frame

        -- Label
        local label = Instance.new("TextLabel")
        label.Name = "Label"
        label.Size = UDim2.fromScale(0.8, 1)
        label.Position = UDim2.fromOffset(8, 0)
        label.BackgroundTransparency = 1
        label.Text = "◈ " .. itemName
        label.TextColor3 = Config.Theme.Text
        label.TextSize = 11
        label.TextXAlignment = Enum.TextXAlignment.Left
        label.Font = Config.Font
        label.Parent = frame

        -- Toggle Button
        local toggleBtn = Instance.new("TextButton")
        toggleBtn.Name = "ToggleButton"
        toggleBtn.Size = UDim2.fromOffset(32, 16)
        toggleBtn.Position = UDim2.fromScale(1, 0) - UDim2.fromOffset(38, 2)
        toggleBtn.BackgroundColor3 = Config.Theme.Background
        toggleBtn.Text = ""
        toggleBtn.Parent = frame

        local toggleCorner = Instance.new("UICorner")
        toggleCorner.CornerRadius = UDim.new(1, 0)
        toggleCorner.Parent = toggleBtn

        -- Indicator
        local toggleIndicator = Instance.new("Frame")
        toggleIndicator.Name = "Indicator"
        toggleIndicator.Size = UDim2.fromOffset(10, 10)
        toggleIndicator.Position = UDim2.fromOffset(3, 3)
        toggleIndicator.BackgroundColor3 = Config.Theme.TextMuted
        toggleIndicator.BackgroundTransparency = 0.5
        toggleIndicator.Parent = toggleBtn

        local indicatorCorner = Instance.new("UICorner")
        indicatorCorner.CornerRadius = UDim.new(1, 0)
        indicatorCorner.Parent = toggleIndicator

        local state = false

        local function UpdateToggle()
            state = not state
            local targetColor = state and Config.Theme.Primary or Config.Theme.Background
            local indicatorColor = state and Config.Theme.Background or Config.Theme.TextMuted

            CreateTween(toggleBtn, {BackgroundColor3 = targetColor}, 0.15):Play()
            CreateTween(toggleIndicator, {
                BackgroundColor3 = indicatorColor,
                BackgroundTransparency = state and 0 or 0.5
            }, 0.15):Play()
            CreateTween(toggleIndicator, {
                Position = state and UDim2.fromOffset(19, 3) or UDim2.fromOffset(3, 3)
            }, 0.2):Play()

            PlaySound()

            local status = state and "✓" or "✗"
            AddLog(string.format("[%s] %s %s", CurrentTab, itemName, status), 
                   state and Config.Theme.Primary or Config.Theme.TextMuted)
        end

        toggleBtn.MouseButton1Click:Connect(UpdateToggle)
        Toggles[itemName] = {
            State = function() return state end,
            Toggle = UpdateToggle
        }

        return frame
    end

    -- Build Content
    local function BuildContent()
        -- Limpar conteúdo
        for _, child in ipairs(ContentFrame:GetChildren()) do
            if child:IsA("Frame") or child:IsA("TextLabel") then
                child:Destroy()
            end
        end

        for _, category in ipairs(Categories) do
            -- Categoria Label
            local catLabel = Instance.new("TextLabel")
            catLabel.Name = "CategoryLabel"
            catLabel.Size = UDim2.fromScale(1, 0.04)
            catLabel.BackgroundTransparency = 1
            catLabel.Text = category.Name
            catLabel.TextColor3 = Config.Theme.Primary
            catLabel.TextSize = 12
            catLabel.TextXAlignment = Enum.TextXAlignment.Left
            catLabel.Font = Config.FontBold
            catLabel.Parent = ContentFrame

            -- Separator
            local sep = Instance.new("Frame")
            sep.Name = "Separator"
            sep.Size = UDim2.fromScale(0.9, 0.001)
            sep.Position = UDim2.fromScale(0.05, 0)
            sep.BackgroundColor3 = Config.Theme.Primary
            sep.BackgroundTransparency = 0.5
            sep.Parent = ContentFrame

            -- Items
            for _, itemName in ipairs(category.Items) do
                CreateToggle(itemName, ContentFrame)
            end
        end
    end

    BuildContent()

    -- ============================================
    -- BOTTOM BAR - TextBox e Executar
    -- ============================================
    local BottomBar = Instance.new("Frame")
    BottomBar.Name = "BottomBar"
    BottomBar.Size = UDim2.fromScale(1, 0.08)
    BottomBar.Position = UDim2.fromScale(0, 0.78)
    BottomBar.BackgroundColor3 = Config.Theme.BackgroundSecondary
    BottomBar.Parent = MainFrame

    local BottomCorner = Instance.new("UICorner")
    BottomCorner.CornerRadius = UDim.new(0, 4)
    BottomCorner.Parent = BottomBar

    -- TextBox
    local TextInput = Instance.new("TextBox")
    TextInput.Name = "TextInput"
    TextInput.Size = UDim2.fromScale(0.7, 0.7)
    TextInput.Position = UDim2.fromOffset(8, 5)
    TextInput.BackgroundColor3 = Config.Theme.BackgroundTertiary
    TextInput.Text = ""
    TextInput.TextColor3 = Config.Theme.Text
    TextInput.TextSize = 11
    TextInput.PlaceholderText = "Digite algo..."
    TextInput.PlaceholderColor3 = Config.Theme.TextMuted
    TextInput.Font = Config.Font
    TextInput.ClearTextOnFocus = false
    TextInput.Parent = BottomBar

    local TextCorner = Instance.new("UICorner")
    TextCorner.CornerRadius = UDim.new(0, 3)
    TextCorner.Parent = TextInput

    local TextStroke = Instance.new("UIStroke")
    TextStroke.Color = Config.Theme.Border
    TextStroke.Thickness = 1
    TextStroke.Parent = TextInput

    -- Execute Button
    local ExecuteButton = Instance.new("TextButton")
    ExecuteButton.Name = "ExecuteButton"
    ExecuteButton.Size = UDim2.fromScale(0.22, 0.7)
    ExecuteButton.Position = UDim2.fromScale(0.76, 0.15)
    ExecuteButton.BackgroundColor3 = Config.Theme.Primary
    ExecuteButton.Text = "▶ Executar"
    ExecuteButton.TextColor3 = Config.Theme.Background
    ExecuteButton.TextSize = 11
    ExecuteButton.Font = Config.FontBold
    ExecuteButton.Parent = BottomBar

    local ExecCorner = Instance.new("UICorner")
    ExecCorner.CornerRadius = UDim.new(0, 3)
    ExecCorner.Parent = ExecuteButton

    -- ============================================
    -- CONSOLE
    -- ============================================
    local ConsoleFrame = Instance.new("Frame")
    ConsoleFrame.Name = "ConsoleFrame"
    ConsoleFrame.Size = UDim2.fromScale(1, 0.14)
    ConsoleFrame.Position = UDim2.fromScale(0, 0.86)
    ConsoleFrame.BackgroundColor3 = Config.Theme.BackgroundSecondary
    ConsoleFrame.Parent = MainFrame

    local ConsoleCorner = Instance.new("UICorner")
    ConsoleCorner.CornerRadius = UDim.new(0, 8)
    ConsoleCorner.Parent = ConsoleFrame

    ConsoleOutput = Instance.new("TextBox")
    ConsoleOutput.Name = "ConsoleOutput"
    ConsoleOutput.Size = UDim2.fromScale(1, 1)
    ConsoleOutput.Position = UDim2.fromOffset(8, 0)
    ConsoleOutput.BackgroundTransparency = 1
    ConsoleOutput.Text = "> Script Loaded..."
    ConsoleOutput.TextColor3 = Config.Theme.TextMuted
    ConsoleOutput.TextSize = 9
    ConsoleOutput.TextXAlignment = Enum.TextXAlignment.Left
    ConsoleOutput.TextYAlignment = Enum.TextYAlignment.Center
    ConsoleOutput.Font = Enum.Font.Code
    ConsoleOutput.ClearTextOnFocus = false
    ConsoleOutput.Parent = ConsoleFrame

    -- ============================================
    -- EVENTOS
    -- ============================================

    -- Fechar
    CloseButton.MouseButton1Click:Connect(function()
        PlaySound()
        local tween = CreateTween(MainFrame, {
            BackgroundTransparency = 1,
            Size = UDim2.fromOffset(0, 0)
        }, 0.3)
        tween:Play()
        tween.Completed:Connect(function()
            MainFrame.Visible = false
            if UIBlur then
                CreateTween(UIBlur, {Size = 0}, 0.3):Play()
            end
        end)
        if UIBlur then
            CreateTween(UIBlur, {Size = 0}, 0.3):Play()
        end
    end)

    -- Drag
    Header.InputBegan:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1 then
            Dragging = true
            DragStart = input.Position
            DragStartPosition = MainFrame.Position
        end
    end)

    UserInputService.InputChanged:Connect(function(input)
        if Dragging and input.UserInputType == Enum.UserInputType.MouseMovement then
            local delta = input.Position - DragStart
            MainFrame.Position = UDim2.new(
                DragStartPosition.X.Scale,
                DragStartPosition.X.Offset + delta.X,
                DragStartPosition.Y.Scale,
                DragStartPosition.Y.Offset + delta.Y
            )
        end
    end)

    UserInputService.InputEnded:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1 then
            Dragging = false
        end
    end)

    -- Tabs
    for tabName, btn in pairs(TabButtons) do
        btn.MouseButton1Click:Connect(function()
            CurrentTab = tabName
            for name, button in pairs(TabButtons) do
                button.BackgroundColor3 = name == tabName and Config.Theme.Primary or Config.Theme.BackgroundSecondary
                button.TextColor3 = name == tabName and Config.Theme.Background or Config.Theme.TextSecondary
            end
            PlaySound()
            AddLog("Tab: " .. tabName, Config.Theme.Primary)
        end)
    end

    -- Execute
    ExecuteButton.MouseButton1Click:Connect(function()
        local text = TextInput.Text
        if text ~= "" then
            PlaySound()
            AddLog("▶ Executando: " .. text, Config.Theme.Primary)
            StatusLabel.Text = "[Car Flipper] Executing..."
            StatusLabel.TextColor3 = Config.Theme.Primary

            -- Simulação
            task.wait(1.2)
            StatusLabel.Text = "[Car Flipper] Idle"
            StatusLabel.TextColor3 = Config.Theme.TextSecondary
            AddLog("✓ Execução concluída", Config.Theme.Primary)
        else
            AddLog("⚠ Digite algo para executar", Config.Theme.Warning)
        end
    end)

    -- Enter
    TextInput.FocusLost:Connect(function(enterPressed)
        if enterPressed then
            ExecuteButton.MouseButton1Click:Fire()
        end
    end)

    -- ============================================
    -- ANIMAÇÃO DE ABERTURA
    -- ============================================
    MainFrame.Visible = true
    MainFrame.BackgroundTransparency = 1
    MainFrame.Size = UDim2.fromOffset(0, 0)

    if UIBlur then
        CreateTween(UIBlur, {Size = 12}, 0.5):Play()
    end

    task.wait(0.1)
    local openTween = CreateTween(MainFrame, {
        BackgroundTransparency = 0.05,
        Size = UDim2.fromOffset(380, 500)
    }, 0.4, Enum.EasingStyle.Back)
    openTween:Play()

    AddLog("✓ Car Flipper Interface carregada!", Config.Theme.Primary)

    -- ============================================
    -- RETORNO
    -- ============================================
    return {
        MainFrame = MainFrame,
        Toggles = Toggles,
        AddLog = AddLog,
        SetStatus = function(text, color)
            StatusLabel.Text = "[Car Flipper] " .. text
            StatusLabel.TextColor3 = color or Config.Theme.TextSecondary
        end,
        GetToggles = function() return Toggles end,
        Config = Config,
    }
end

-- ============================================
-- INICIALIZAÇÃO
-- ============================================
local UI = CreateUI()

-- Exportar para uso global
_G.CarFlipperUI = UI

print("✅ Car Flipper Interface carregada com sucesso!")

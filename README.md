--[[
    Interface Premium - LocalScript
    Roblox UI Moderna e Elegante
    Criado com organização profissional
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
        Background = Color3.fromHex("111111"),
        BackgroundSecondary = Color3.fromHex("1A1A1A"),
        BackgroundTertiary = Color3.fromHex("222222"),
        Primary = Color3.fromHex("2B7AFF"),
        PrimaryDark = Color3.fromHex("1A5FD9"),
        PrimaryLight = Color3.fromHex("4B8FFF"),
        Text = Color3.fromHex("FFFFFF"),
        TextSecondary = Color3.fromHex("888888"),
        Border = Color3.fromHex("333333"),
        Success = Color3.fromHex("00D68F"),
        Danger = Color3.fromHex("FF3D71"),
        Warning = Color3.fromHex("FFAA00"),
    },
    Font = Enum.Font.Gotham,
    BlurEnabled = true,
    SoundEnabled = true,
}

-- ============================================
-- VARIÁVEIS GLOBAIS
-- ============================================
local MainFrame
local StatusLabel
local ConsoleOutput
local TextInput
local UIBlur
local Dragging = false
local DragStart = Vector2.new()
local DragStartPosition = UDim2.new()
local Logs = {}

-- ============================================
-- FUNÇÕES AUXILIARES
-- ============================================
local function CreateTween(object, properties, duration, style)
    style = style or Enum.EasingStyle.Quad
    local tweenInfo = TweenInfo.new(duration or 0.3, style)
    local tween = TweenService:Create(object, tweenInfo, properties)
    return tween
end

local function PlaySound(soundId)
    if not Config.SoundEnabled then return end
    local sound = Instance.new("Sound")
    sound.SoundId = soundId or "rbxassetid://9120386751"
    sound.Volume = 0.3
    sound.Parent = MainFrame or game:GetService("CoreGui")
    sound:Play()
    game:GetService("Debris"):AddItem(sound, 1)
end

local function AddLog(text, color)
    color = color or Config.Theme.Text
    table.insert(Logs, {Text = text, Color = color})
    if #Logs > 100 then
        table.remove(Logs, 1)
    end
    UpdateConsole()
end

local function UpdateConsole()
    if not ConsoleOutput then return end
    local text = ""
    for i = math.max(1, #Logs - 15), #Logs do
        local log = Logs[i]
        if log then
            text = text .. log.Text .. "\n"
        end
    end
    ConsoleOutput.Text = text
end

local function CreateUIScale(obj, scale)
    local scaleObj = Instance.new("UIScale")
    scaleObj.Scale = scale or 1
    scaleObj.Parent = obj
    return scaleObj
end

-- ============================================
-- CRIAÇÃO DA INTERFACE
-- ============================================
local function CreateUI()
    local ScreenGui = Instance.new("ScreenGui")
    ScreenGui.Name = "PremiumInterface"
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
    MainFrame.Size = UDim2.fromOffset(450, 600)
    MainFrame.Position = UDim2.fromScale(0.5, 0.5) - UDim2.fromOffset(225, 300)
    MainFrame.BackgroundColor3 = Config.Theme.Background
    MainFrame.BackgroundTransparency = 0.95
    MainFrame.Parent = ScreenGui
    MainFrame.ClipsDescendants = true

    -- Sombra
    local Shadow = Instance.new("ImageLabel")
    Shadow.Name = "Shadow"
    Shadow.Size = UDim2.fromScale(1, 1)
    Shadow.Position = UDim2.fromOffset(10, 10)
    Shadow.BackgroundColor3 = Color3.fromHex("000000")
    Shadow.BackgroundTransparency = 0.5
    Shadow.Image = "rbxassetid://1313878513"
    Shadow.ImageColor3 = Color3.fromHex("000000")
    Shadow.ImageTransparency = 0.7
    Shadow.ZIndex = 0
    Shadow.Parent = MainFrame

    -- UI Corner
    local Corner = Instance.new("UICorner")
    Corner.CornerRadius = UDim.new(0, 12)
    Corner.Parent = MainFrame

    -- UI Stroke
    local Stroke = Instance.new("UIStroke")
    Stroke.Color = Config.Theme.Border
    Stroke.Thickness = 1.5
    Stroke.Parent = MainFrame

    -- UIScale
    CreateUIScale(MainFrame, 0.9)

    -- ============================================
    -- TOP BAR
    -- ============================================
    local TopBar = Instance.new("Frame")
    TopBar.Name = "TopBar"
    TopBar.Size = UDim2.fromScale(1, 0.08)
    TopBar.BackgroundColor3 = Config.Theme.Primary
    TopBar.Parent = MainFrame

    local TopCorner = Instance.new("UICorner")
    TopCorner.CornerRadius = UDim.new(0, 12)
    TopCorner.Parent = TopBar

    local TopBarStroke = Instance.new("UIStroke")
    TopBarStroke.Color = Config.Theme.PrimaryDark
    TopBarStroke.Thickness = 1
    TopBarStroke.Parent = TopBar

    -- Título
    local Title = Instance.new("TextLabel")
    Title.Name = "Title"
    Title.Size = UDim2.fromScale(0.8, 1)
    Title.Position = UDim2.fromOffset(15, 0)
    Title.BackgroundTransparency = 1
    Title.Text = "⚡ Premium Executor"
    Title.TextColor3 = Config.Theme.Text
    Title.TextSize = 18
    Title.TextXAlignment = Enum.TextXAlignment.Left
    Title.Font = Config.Font
    Title.Parent = TopBar

    -- Botão Fechar
    local CloseButton = Instance.new("TextButton")
    CloseButton.Name = "CloseButton"
    CloseButton.Size = UDim2.fromOffset(30, 30)
    CloseButton.Position = UDim2.fromScale(1, 0) - UDim2.fromOffset(35, 5)
    CloseButton.BackgroundColor3 = Config.Theme.PrimaryDark
    CloseButton.Text = "✕"
    CloseButton.TextColor3 = Config.Theme.Text
    CloseButton.TextSize = 18
    CloseButton.TextScaled = true
    CloseButton.Font = Config.Font
    CloseButton.Parent = TopBar

    local CloseCorner = Instance.new("UICorner")
    CloseCorner.CornerRadius = UDim.new(0, 6)
    CloseCorner.Parent = CloseButton

    -- ============================================
    -- ABAS
    -- ============================================
    local TabBar = Instance.new("Frame")
    TabBar.Name = "TabBar"
    TabBar.Size = UDim2.fromScale(1, 0.06)
    TabBar.Position = UDim2.fromScale(0, 0.08)
    TabBar.BackgroundColor3 = Config.Theme.BackgroundSecondary
    TabBar.Parent = MainFrame

    local TabCorner = Instance.new("UICorner")
    TabCorner.CornerRadius = UDim.new(0, 6)
    TabCorner.Parent = TabBar

    -- Botões das abas
    local Tabs = {"Auto", "Run Once"}
    local TabButtons = {}
    local CurrentTab = "Auto"
    local TabOptions = {}

    for i, tabName in ipairs(Tabs) do
        local btn = Instance.new("TextButton")
        btn.Name = tabName
        btn.Size = UDim2.fromScale(0.5, 1)
        btn.Position = UDim2.fromScale((i-1) * 0.5, 0)
        btn.BackgroundColor3 = i == 1 and Config.Theme.Primary or Config.Theme.BackgroundSecondary
        btn.Text = tabName
        btn.TextColor3 = i == 1 and Config.Theme.Text or Config.Theme.TextSecondary
        btn.TextSize = 14
        btn.Font = Config.Font
        btn.Parent = TabBar

        local btnCorner = Instance.new("UICorner")
        btnCorner.CornerRadius = UDim.new(0, 6)
        btnCorner.Parent = btn

        TabButtons[tabName] = btn
        TabOptions[tabName] = {}
    end

    -- ============================================
    -- STATUS
    -- ============================================
    local StatusBar = Instance.new("Frame")
    StatusBar.Name = "StatusBar"
    StatusBar.Size = UDim2.fromScale(1, 0.04)
    StatusBar.Position = UDim2.fromScale(0, 0.14)
    StatusBar.BackgroundColor3 = Config.Theme.BackgroundSecondary
    StatusBar.Parent = MainFrame

    StatusLabel = Instance.new("TextLabel")
    StatusLabel.Name = "StatusLabel"
    StatusLabel.Size = UDim2.fromScale(1, 1)
    StatusLabel.Position = UDim2.fromOffset(10, 0)
    StatusLabel.BackgroundTransparency = 1
    StatusLabel.Text = "Status: Idle"
    StatusLabel.TextColor3 = Config.Theme.TextSecondary
    StatusLabel.TextSize = 12
    StatusLabel.TextXAlignment = Enum.TextXAlignment.Left
    StatusLabel.Font = Config.Font
    StatusLabel.Parent = StatusBar

    -- ============================================
    -- CONTEÚDO (Categorias e Toggles)
    -- ============================================
    local ContentFrame = Instance.new("ScrollingFrame")
    ContentFrame.Name = "ContentFrame"
    ContentFrame.Size = UDim2.fromScale(1, 0.65)
    ContentFrame.Position = UDim2.fromScale(0, 0.18)
    ContentFrame.BackgroundColor3 = Config.Theme.BackgroundSecondary
    ContentFrame.BackgroundTransparency = 0.5
    ContentFrame.Parent = MainFrame
    ContentFrame.ScrollBarThickness = 4
    ContentFrame.ScrollBarImageColor3 = Config.Theme.Primary

    local ContentCorner = Instance.new("UICorner")
    ContentCorner.CornerRadius = UDim.new(0, 8)
    ContentCorner.Parent = ContentFrame

    -- UIListLayout para o conteúdo
    local ContentLayout = Instance.new("UIListLayout")
    ContentLayout.Name = "ContentLayout"
    ContentLayout.FillDirection = Enum.FillDirection.Vertical
    ContentLayout.SortOrder = Enum.SortOrder.LayoutOrder
    ContentLayout.Padding = UDim.new(0, 5)
    ContentLayout.Parent = ContentFrame

    -- ============================================
    -- CATEGORIAS E BOTÕES
    -- ============================================
    local Categories = {
        Base = {
            Label = "⚙️ Base",
            Items = {
                "Claim Cash", "Claim Parts", "Buy Upgrades",
                "Claim Containers", "Open Containers", "Claim Quests",
                "Deliver Junk"
            }
        },
        Cars = {
            Label = "🚗 Cars",
            Items = {
                "Fix Cars", "Sell Cars", "Update Stands", "Buy Cheapest"
            }
        },
        Rewards = {
            Label = "🎁 Rewards",
            Items = {
                "Claim Playtime Rewards", "Claim Daily Rewards"
            }
        }
    }

    local Toggles = {}

    local function CreateToggle(category, itemName, parent)
        local frame = Instance.new("Frame")
        frame.Name = itemName
        frame.Size = UDim2.fromScale(1, 0.08)
        frame.BackgroundColor3 = Config.Theme.BackgroundTertiary
        frame.BackgroundTransparency = 0.3
        frame.Parent = parent

        local frameCorner = Instance.new("UICorner")
        frameCorner.CornerRadius = UDim.new(0, 6)
        frameCorner.Parent = frame

        local label = Instance.new("TextLabel")
        label.Name = "Label"
        label.Size = UDim2.fromScale(0.8, 1)
        label.Position = UDim2.fromOffset(12, 0)
        label.BackgroundTransparency = 1
        label.Text = itemName
        label.TextColor3 = Config.Theme.Text
        label.TextSize = 13
        label.TextXAlignment = Enum.TextXAlignment.Left
        label.Font = Config.Font
        label.Parent = frame

        local toggleBtn = Instance.new("TextButton")
        toggleBtn.Name = "ToggleButton"
        toggleBtn.Size = UDim2.fromOffset(40, 22)
        toggleBtn.Position = UDim2.fromScale(1, 0) - UDim2.fromOffset(50, 4)
        toggleBtn.BackgroundColor3 = Config.Theme.Background
        toggleBtn.Text = ""
        toggleBtn.Parent = frame

        local toggleCorner = Instance.new("UICorner")
        toggleCorner.CornerRadius = UDim.new(1, 0)
        toggleCorner.Parent = toggleBtn

        local toggleIndicator = Instance.new("Frame")
        toggleIndicator.Name = "Indicator"
        toggleIndicator.Size = UDim2.fromOffset(16, 16)
        toggleIndicator.Position = UDim2.fromOffset(3, 3)
        toggleIndicator.BackgroundColor3 = Config.Theme.TextSecondary
        toggleIndicator.BackgroundTransparency = 0.5
        toggleIndicator.Parent = toggleBtn

        local indicatorCorner = Instance.new("UICorner")
        indicatorCorner.CornerRadius = UDim.new(1, 0)
        indicatorCorner.Parent = toggleIndicator

        local state = false

        local function UpdateToggle()
            state = not state
            local targetColor = state and Config.Theme.Primary or Config.Theme.Background
            local indicatorColor = state and Config.Theme.Text or Config.Theme.TextSecondary

            CreateTween(toggleBtn, {BackgroundColor3 = targetColor}, 0.2):Play()
            CreateTween(toggleIndicator, {BackgroundColor3 = indicatorColor, BackgroundTransparency = state and 0 or 0.5}, 0.2):Play()
            CreateTween(toggleIndicator, {Position = state and UDim2.fromOffset(21, 3) or UDim2.fromOffset(3, 3)}, 0.3):Play()

            PlaySound()

            AddLog(string.format("[%s] %s: %s", CurrentTab, itemName, state and "ON" or "OFF"), state and Config.Theme.Success or Config.Theme.TextSecondary)
        end

        toggleBtn.MouseButton1Click:Connect(UpdateToggle)
        Toggles[itemName] = {
            State = function() return state end,
            Toggle = UpdateToggle,
            Button = toggleBtn
        }

        return frame
    end

    -- Criar categorias e toggles
    local function BuildContent()
        -- Limpar conteúdo anterior
        for _, child in ipairs(ContentFrame:GetChildren()) do
            if child:IsA("Frame") or child:IsA("TextLabel") then
                child:Destroy()
            end
        end

        for _, categoryData in pairs(Categories) do
            -- Categoria
            local categoryLabel = Instance.new("TextLabel")
            categoryLabel.Name = "CategoryLabel"
            categoryLabel.Size = UDim2.fromScale(1, 0.04)
            categoryLabel.BackgroundTransparency = 1
            categoryLabel.Text = categoryData.Label
            categoryLabel.TextColor3 = Config.Theme.Primary
            categoryLabel.TextSize = 14
            categoryLabel.TextXAlignment = Enum.TextXAlignment.Left
            categoryLabel.Font = Config.Font
            categoryLabel.Parent = ContentFrame

            local separator = Instance.new("Frame")
            separator.Name = "Separator"
            separator.Size = UDim2.fromScale(1, 0.002)
            separator.BackgroundColor3 = Config.Theme.Primary
            separator.BackgroundTransparency = 0.5
            separator.Parent = ContentFrame

            for _, itemName in ipairs(categoryData.Items) do
                local toggleFrame = CreateToggle(categoryData.Label, itemName, ContentFrame)
                -- Armazenar para referência
                if not TabOptions[CurrentTab] then TabOptions[CurrentTab] = {} end
                table.insert(TabOptions[CurrentTab], toggleFrame)
            end
        end
    end

    BuildContent()

    -- ============================================
    -- TEXTBOX E BOTÃO EXECUTAR
    -- ============================================
    local BottomBar = Instance.new("Frame")
    BottomBar.Name = "BottomBar"
    BottomBar.Size = UDim2.fromScale(1, 0.10)
    BottomBar.Position = UDim2.fromScale(0, 0.83)
    BottomBar.BackgroundColor3 = Config.Theme.BackgroundSecondary
    BottomBar.Parent = MainFrame

    local BottomCorner = Instance.new("UICorner")
    BottomCorner.CornerRadius = UDim.new(0, 8)
    BottomCorner.Parent = BottomBar

    -- TextBox
    TextInput = Instance.new("TextBox")
    TextInput.Name = "TextInput"
    TextInput.Size = UDim2.fromScale(0.75, 0.7)
    TextInput.Position = UDim2.fromOffset(10, 8)
    TextInput.BackgroundColor3 = Config.Theme.BackgroundTertiary
    TextInput.Text = ""
    TextInput.TextColor3 = Config.Theme.Text
    TextInput.TextSize = 13
    TextInput.PlaceholderText = "Digite algo..."
    TextInput.PlaceholderColor3 = Config.Theme.TextSecondary
    TextInput.Font = Config.Font
    TextInput.ClearTextOnFocus = false
    TextInput.Parent = BottomBar

    local TextCorner = Instance.new("UICorner")
    TextCorner.CornerRadius = UDim.new(0, 6)
    TextCorner.Parent = TextInput

    local TextStroke = Instance.new("UIStroke")
    TextStroke.Color = Config.Theme.Border
    TextStroke.Thickness = 1
    TextStroke.Parent = TextInput

    -- Botão Executar
    local ExecuteButton = Instance.new("TextButton")
    ExecuteButton.Name = "ExecuteButton"
    ExecuteButton.Size = UDim2.fromScale(0.18, 0.7)
    ExecuteButton.Position = UDim2.fromScale(0.8, 0.15)
    ExecuteButton.BackgroundColor3 = Config.Theme.Primary
    ExecuteButton.Text = "▶ Executar"
    ExecuteButton.TextColor3 = Config.Theme.Text
    ExecuteButton.TextSize = 14
    ExecuteButton.Font = Config.Font
    ExecuteButton.Parent = BottomBar

    local ExecCorner = Instance.new("UICorner")
    ExecCorner.CornerRadius = UDim.new(0, 6)
    ExecCorner.Parent = ExecuteButton

    -- ============================================
    -- CONSOLE
    -- ============================================
    local ConsoleFrame = Instance.new("Frame")
    ConsoleFrame.Name = "ConsoleFrame"
    ConsoleFrame.Size = UDim2.fromScale(1, 0.07)
    ConsoleFrame.Position = UDim2.fromScale(0, 0.93)
    ConsoleFrame.BackgroundColor3 = Config.Theme.BackgroundSecondary
    ConsoleFrame.Parent = MainFrame

    local ConsoleCorner = Instance.new("UICorner")
    ConsoleCorner.CornerRadius = UDim.new(0, 8)
    ConsoleCorner.Parent = ConsoleFrame

    ConsoleOutput = Instance.new("TextBox")
    ConsoleOutput.Name = "ConsoleOutput"
    ConsoleOutput.Size = UDim2.fromScale(1, 1)
    ConsoleOutput.Position = UDim2.fromOffset(10, 0)
    ConsoleOutput.BackgroundTransparency = 1
    ConsoleOutput.Text = "> Script Loaded..."
    ConsoleOutput.TextColor3 = Config.Theme.TextSecondary
    ConsoleOutput.TextSize = 11
    ConsoleOutput.TextXAlignment = Enum.TextXAlignment.Left
    ConsoleOutput.TextYAlignment = Enum.TextYAlignment.Center
    ConsoleOutput.Font = Config.Font
    ConsoleOutput.ClearTextOnFocus = false
    ConsoleOutput.Parent = ConsoleFrame

    -- ============================================
    -- FUNCIONALIDADES
    -- ============================================

    -- Botão Fechar
    CloseButton.MouseButton1Click:Connect(function()
        PlaySound()
        local tween = CreateTween(MainFrame, {BackgroundTransparency = 1, Size = UDim2.fromOffset(0, 0)}, 0.3)
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

    -- Arrastar
    TopBar.InputBegan:Connect(function(input)
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

    -- Abas
    for tabName, btn in pairs(TabButtons) do
        btn.MouseButton1Click:Connect(function()
            CurrentTab = tabName
            for name, button in pairs(TabButtons) do
                button.BackgroundColor3 = name == tabName and Config.Theme.Primary or Config.Theme.BackgroundSecondary
                button.TextColor3 = name == tabName and Config.Theme.Text or Config.Theme.TextSecondary
            end
            PlaySound()
            AddLog("Switched to: " .. tabName, Config.Theme.Primary)
        end)
    end

    -- Executar
    ExecuteButton.MouseButton1Click:Connect(function()
        local text = TextInput.Text
        if text ~= "" then
            PlaySound()
            AddLog("Executando: " .. text, Config.Theme.Primary)
            StatusLabel.Text = "Status: Executing..."
            StatusLabel.TextColor3 = Config.Theme.Primary

            -- Simular execução
            task.wait(1)
            StatusLabel.Text = "Status: Idle"
            StatusLabel.TextColor3 = Config.Theme.TextSecondary
            AddLog("Execução concluída", Config.Theme.Success)
        else
            AddLog("Erro: Digite algo para executar", Config.Theme.Danger)
        end
    end)

    -- Enter no TextBox
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
        CreateTween(UIBlur, {Size = 20}, 0.5):Play()
    end

    task.wait(0.1)
    local openTween = CreateTween(MainFrame, {
        BackgroundTransparency = 0.05,
        Size = UDim2.fromOffset(450, 600)
    }, 0.4, Enum.EasingStyle.Back)
    openTween:Play()

    AddLog("Interface carregada com sucesso!", Config.Theme.Success)

    -- ============================================
    -- RETORNAR REFERÊNCIAS
    -- ============================================
    return {
        MainFrame = MainFrame,
        Toggles = Toggles,
        AddLog = AddLog,
        SetStatus = function(text, color)
            StatusLabel.Text = "Status: " .. text
            StatusLabel.TextColor3 = color or Config.Theme.TextSecondary
        end,
        GetToggles = function() return Toggles end,
    }
end

-- ============================================
-- INICIALIZAÇÃO
-- ============================================
local UI = CreateUI()

-- Exemplo de uso:
-- UI.AddLog("Teste", Color3.fromHex("FFAA00"))
-- UI.SetStatus("Running", UI.Config.Theme.Success)

-- ============================================
-- EXPORTAÇÃO (Para uso externo)
-- ============================================
_G.PremiumUI = UI

print("✅ Premium Interface carregada com sucesso!")

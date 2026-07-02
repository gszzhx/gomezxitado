--[[
    ═══════════════════════════════════════════════════════════════
    Car Flipper - GomezXitado
    Interface Profissional com Tema Escuro
    Versão: 1.0.0
    ═══════════════════════════════════════════════════════════════
    
    ESTRUTURA:
    • ScreenGui (Principal)
    │   ├── MainFrame (Janela Principal)
    │   │   ├── TitleBar (Barra de Título)
    │   │   │   ├── Title (Texto do Título)
    │   │   │   └── CloseButton (Botão Fechar)
    │   │   ├── TabContainer (Container de Abas)
    │   │   │   ├── AutoTab (Aba Auto)
    │   │   │   └── RunOnceTab (Aba Run Once)
    │   │   ├── SearchBox (Campo de Pesquisa)
    │   │   │   ├── TextBox (Entrada de Texto)
    │   │   │   └── SendButton (Botão Enviar)
    │   │   ├── StatusLabel (Label de Status)
    │   │   ├── SectionDivider (Divisor de Seção)
    │   │   ├── BaseSection (Seção Base)
    │   │   │   ├── ClaimCash (Checkbox)
    │   │   │   ├── ClaimParts (Checkbox)
    │   │   │   ├── BuyUpgrades (Checkbox)
    │   │   │   ├── ClaimContainers (Checkbox)
    │   │   │   ├── OpenContainers (Checkbox)
    │   │   │   ├── ClaimQuests (Checkbox)
    │   │   │   └── DeliverJunk (Checkbox)
    │   │   ├── CarsSection (Seção Cars)
    │   │   │   ├── FixCars (Checkbox)
    │   │   │   ├── SellFixedCars (Checkbox)
    │   │   │   ├── UpdateStands (Checkbox)
    │   │   │   └── BuyFixSellCheapest (Checkbox)
    │   │   └── RewardsSection (Seção Rewards)
    │   │       ├── ClaimPlaytimeRewards (Checkbox)
    │   │       └── ClaimDailyRewards (Checkbox)
    └── (UI Components: UICorner, UIStroke, UIPadding)
    
    ═══════════════════════════════════════════════════════════════
--]]

-- ═══════════════════════════════════════════════════════════════
-- 1. CONFIGURAÇÕES E CONSTANTES
-- ═══════════════════════════════════════════════════════════════

local Players = game:GetService("Players")
local LocalPlayer = Players.LocalPlayer
local TweenService = game:GetService("TweenService")
local UserInputService = game:GetService("UserInputService")

-- Cores do Tema (Dark Theme)
local COLORS = {
    Background = Color3.fromRGB(20, 20, 25),
    Frame = Color3.fromRGB(30, 30, 38),
    TitleBar = Color3.fromRGB(40, 40, 48),
    Text = Color3.fromRGB(220, 220, 230),
    TextDim = Color3.fromRGB(150, 150, 165),
    Accent = Color3.fromRGB(255, 200, 50),
    AccentHover = Color3.fromRGB(255, 215, 75),
    CheckboxBG = Color3.fromRGB(45, 45, 55),
    CheckboxChecked = Color3.fromRGB(255, 200, 50),
    Border = Color3.fromRGB(60, 60, 75),
    Divider = Color3.fromRGB(50, 50, 62),
    Shadow = Color3.fromRGB(0, 0, 0),
    SearchBox = Color3.fromRGB(38, 38, 48),
}

-- ═══════════════════════════════════════════════════════════════
-- 2. CRIAÇÃO DO GUI PRINCIPAL
-- ═══════════════════════════════════════════════════════════════

-- 2.1 ScreenGui
local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Name = "CarFlipperGUI"
ScreenGui.Parent = LocalPlayer:WaitForChild("PlayerGui")
ScreenGui.ResetOnSpawn = false

-- 2.2 MainFrame (Janela Principal)
local MainFrame = Instance.new("Frame")
MainFrame.Name = "MainFrame"
MainFrame.Size = UDim2.new(0, 420, 0, 580)
MainFrame.Position = UDim2.new(0.5, -210, 0.5, -290)
MainFrame.BackgroundColor3 = COLORS.Background
MainFrame.BackgroundTransparency = 0
MainFrame.Parent = ScreenGui

-- Sombra (Shadow)
local Shadow = Instance.new("Frame")
Shadow.Name = "Shadow"
Shadow.Size = UDim2.new(1, 12, 1, 12)
Shadow.Position = UDim2.new(0, -6, 0, -6)
Shadow.BackgroundColor3 = COLORS.Shadow
Shadow.BackgroundTransparency = 0.6
Shadow.BorderSizePixel = 0
Shadow.Parent = MainFrame

local ShadowCorner = Instance.new("UICorner")
ShadowCorner.CornerRadius = UDim.new(0, 14)
ShadowCorner.Parent = Shadow

-- 2.3 UI Components (MainFrame)
local MainCorner = Instance.new("UICorner")
MainCorner.CornerRadius = UDim.new(0, 12)
MainCorner.Parent = MainFrame

local MainStroke = Instance.new("UIStroke")
MainStroke.Color = COLORS.Border
MainStroke.Thickness = 1
MainStroke.Parent = MainFrame

-- ═══════════════════════════════════════════════════════════════
-- 3. BARRA DE TÍTULO (TITLE BAR)
-- ═══════════════════════════════════════════════════════════════

-- 3.1 TitleBar
local TitleBar = Instance.new("Frame")
TitleBar.Name = "TitleBar"
TitleBar.Size = UDim2.new(1, 0, 0, 44)
TitleBar.BackgroundColor3 = COLORS.TitleBar
TitleBar.BackgroundTransparency = 0
TitleBar.Parent = MainFrame

local TitleBarCorner = Instance.new("UICorner")
TitleBarCorner.CornerRadius = UDim.new(0, 12)
TitleBarCorner.Parent = TitleBar

-- 3.2 Title
local Title = Instance.new("TextLabel")
Title.Name = "Title"
Title.Size = UDim2.new(1, -50, 1, 0)
Title.Position = UDim2.new(0, 15, 0, 0)
Title.BackgroundTransparency = 1
Title.Text = "Car Flipper - GomezXitado"
Title.TextColor3 = COLORS.Text
Title.TextSize = 16
Title.Font = Enum.Font.GothamSemibold
Title.TextXAlignment = Enum.TextXAlignment.Left
Title.TextYAlignment = Enum.TextYAlignment.Center
Title.Parent = TitleBar

-- 3.3 CloseButton (X)
local CloseButton = Instance.new("ImageButton")
CloseButton.Name = "CloseButton"
CloseButton.Size = UDim2.new(0, 30, 0, 30)
CloseButton.Position = UDim2.new(1, -38, 0.5, -15)
CloseButton.BackgroundColor3 = COLORS.TitleBar
CloseButton.BackgroundTransparency = 0.5
CloseButton.Image = "rbxassetid://10747329800" -- Ícone X
CloseButton.ImageColor3 = COLORS.TextDim
CloseButton.Parent = TitleBar

local CloseCorner = Instance.new("UICorner")
CloseCorner.CornerRadius = UDim.new(1, 0)
CloseCorner.Parent = CloseButton

-- Tween para CloseButton
CloseButton.MouseEnter:Connect(function()
    TweenService:Create(CloseButton, TweenInfo.new(0.15), {
        BackgroundColor3 = Color3.fromRGB(200, 40, 40),
        ImageColor3 = Color3.fromRGB(255, 255, 255)
    }):Play()
end)

CloseButton.MouseLeave:Connect(function()
    TweenService:Create(CloseButton, TweenInfo.new(0.15), {
        BackgroundColor3 = COLORS.TitleBar,
        ImageColor3 = COLORS.TextDim
    }):Play()
end)

CloseButton.MouseButton1Click:Connect(function()
    TweenService:Create(MainFrame, TweenInfo.new(0.25, Enum.EasingStyle.Quint), {
        Size = UDim2.new(0, 420, 0, 0),
        Position = UDim2.new(0.5, -210, 0.5, 0)
    }):Play()
    task.wait(0.25)
    ScreenGui.Enabled = false
end)

-- ═══════════════════════════════════════════════════════════════
-- 4. ABAS (TABS)
-- ═══════════════════════════════════════════════════════════════

-- 4.1 TabContainer
local TabContainer = Instance.new("Frame")
TabContainer.Name = "TabContainer"
TabContainer.Size = UDim2.new(1, 0, 0, 44)
TabContainer.Position = UDim2.new(0, 0, 0, 44)
TabContainer.BackgroundTransparency = 1
TabContainer.Parent = MainFrame

-- 4.2 AutoTab
local AutoTab = Instance.new("TextButton")
AutoTab.Name = "AutoTab"
AutoTab.Size = UDim2.new(0.5, -1, 1, -4)
AutoTab.Position = UDim2.new(0, 0, 0, 2)
AutoTab.BackgroundColor3 = COLORS.Frame
AutoTab.Text = "Auto"
AutoTab.TextColor3 = COLORS.Text
AutoTab.TextSize = 14
AutoTab.Font = Enum.Font.GothamMedium
AutoTab.Parent = TabContainer

local AutoTabCorner = Instance.new("UICorner")
AutoTabCorner.CornerRadius = UDim.new(0, 8)
AutoTabCorner.Parent = AutoTab

-- 4.3 RunOnceTab
local RunOnceTab = Instance.new("TextButton")
RunOnceTab.Name = "RunOnceTab"
RunOnceTab.Size = UDim2.new(0.5, -1, 1, -4)
RunOnceTab.Position = UDim2.new(0.5, 1, 0, 2)
RunOnceTab.BackgroundColor3 = COLORS.Frame
RunOnceTab.Text = "Run Once"
RunOnceTab.TextColor3 = COLORS.Text
RunOnceTab.TextSize = 14
RunOnceTab.Font = Enum.Font.GothamMedium
RunOnceTab.Parent = TabContainer

local RunOnceTabCorner = Instance.new("UICorner")
RunOnceTabCorner.CornerRadius = UDim.new(0, 8)
RunOnceTabCorner.Parent = RunOnceTab

-- Estado das Abas
local ActiveTab = "Auto"

-- Função para atualizar abas
local function UpdateTabs(active)
    ActiveTab = active
    
    -- AutoTab
    TweenService:Create(AutoTab, TweenInfo.new(0.2), {
        BackgroundColor3 = (active == "Auto") and COLORS.Accent or COLORS.Frame,
        TextColor3 = (active == "Auto") and COLORS.Background or COLORS.Text
    }):Play()
    
    -- RunOnceTab
    TweenService:Create(RunOnceTab, TweenInfo.new(0.2), {
        BackgroundColor3 = (active == "RunOnce") and COLORS.Accent or COLORS.Frame,
        TextColor3 = (active == "RunOnce") and COLORS.Background or COLORS.Text
    }):Play()
end

-- Eventos das Abas
AutoTab.MouseButton1Click:Connect(function() UpdateTabs("Auto") end)
RunOnceTab.MouseButton1Click:Connect(function() UpdateTabs("RunOnce") end)

-- Inicializar com Auto ativo
UpdateTabs("Auto")

-- ═══════════════════════════════════════════════════════════════
-- 5. SEARCH BOX (TEXTBOX)
-- ═══════════════════════════════════════════════════════════════

-- 5.1 SearchBox Container
local SearchBox = Instance.new("Frame")
SearchBox.Name = "SearchBox"
SearchBox.Size = UDim2.new(1, -20, 0, 38)
SearchBox.Position = UDim2.new(0, 10, 0, 88)
SearchBox.BackgroundTransparency = 1
SearchBox.Parent = MainFrame

-- 5.2 TextBox
local SearchTextBox = Instance.new("TextBox")
SearchTextBox.Name = "SearchTextBox"
SearchTextBox.Size = UDim2.new(0.8, -5, 1, 0)
SearchTextBox.Position = UDim2.new(0, 0, 0, 0)
SearchTextBox.BackgroundColor3 = COLORS.SearchBox
SearchTextBox.Text = ""
SearchTextBox.PlaceholderText = "Digite algo..."
SearchTextBox.PlaceholderColor3 = COLORS.TextDim
SearchTextBox.TextColor3 = COLORS.Text
SearchTextBox.TextSize = 13
SearchTextBox.Font = Enum.Font.Gotham
SearchTextBox.ClearTextOnFocus = false
SearchTextBox.Parent = SearchBox

local SearchCorner = Instance.new("UICorner")
SearchCorner.CornerRadius = UDim.new(0, 8)
SearchCorner.Parent = SearchTextBox

local SearchStroke = Instance.new("UIStroke")
SearchStroke.Color = COLORS.Border
SearchStroke.Thickness = 1
SearchStroke.Parent = SearchTextBox

-- 5.3 SendButton
local SendButton = Instance.new("TextButton")
SendButton.Name = "SendButton"
SendButton.Size = UDim2.new(0.2, -5, 1, 0)
SendButton.Position = UDim2.new(0.8, 10, 0, 0)
SendButton.BackgroundColor3 = COLORS.Accent
SendButton.Text = "Enviar"
SendButton.TextColor3 = COLORS.Background
SendButton.TextSize = 13
SendButton.Font = Enum.Font.GothamMedium
SendButton.Parent = SearchBox

local SendCorner = Instance.new("UICorner")
SendCorner.CornerRadius = UDim.new(0, 8)
SendCorner.Parent = SendButton

-- Tween para SendButton
SendButton.MouseEnter:Connect(function()
    TweenService:Create(SendButton, TweenInfo.new(0.15), {
        BackgroundColor3 = COLORS.AccentHover
    }):Play()
end)

SendButton.MouseLeave:Connect(function()
    TweenService:Create(SendButton, TweenInfo.new(0.15), {
        BackgroundColor3 = COLORS.Accent
    }):Play()
end)

SendButton.MouseButton1Click:Connect(function()
    local text = SearchTextBox.Text
    if text ~= "" then
        print("[Car Flipper] Enviado:", text)
        SearchTextBox.Text = ""
        -- Aqui você pode adicionar a lógica para processar o texto
    end
end)

-- ═══════════════════════════════════════════════════════════════
-- 6. STATUS LABEL
-- ═══════════════════════════════════════════════════════════════

local StatusLabel = Instance.new("TextLabel")
StatusLabel.Name = "StatusLabel"
StatusLabel.Size = UDim2.new(1, -20, 0, 24)
StatusLabel.Position = UDim2.new(0, 10, 0, 132)
StatusLabel.BackgroundTransparency = 1
StatusLabel.Text = "[Car Flipper] Idle"
StatusLabel.TextColor3 = COLORS.Text
StatusLabel.TextSize = 13
StatusLabel.Font = Enum.Font.GothamMedium
StatusLabel.TextXAlignment = Enum.TextXAlignment.Left
StatusLabel.TextYAlignment = Enum.TextYAlignment.Center
StatusLabel.Parent = MainFrame

-- ═══════════════════════════════════════════════════════════════
-- 7. DIVISOR DE SEÇÃO
-- ═══════════════════════════════════════════════════════════════

local function CreateDivider(yPosition)
    local Divider = Instance.new("Frame")
    Divider.Name = "Divider"
    Divider.Size = UDim2.new(0.9, 0, 0, 1)
    Divider.Position = UDim2.new(0.05, 0, 0, yPosition)
    Divider.BackgroundColor3 = COLORS.Divider
    Divider.BackgroundTransparency = 0
    Divider.BorderSizePixel = 0
    Divider.Parent = MainFrame
    return Divider
end

local function CreateSectionTitle(title, yPosition)
    local Label = Instance.new("TextLabel")
    Label.Name = "SectionTitle"
    Label.Size = UDim2.new(0.9, 0, 0, 22)
    Label.Position = UDim2.new(0.05, 0, 0, yPosition)
    Label.BackgroundTransparency = 1
    Label.Text = title
    Label.TextColor3 = COLORS.TextDim
    Label.TextSize = 12
    Label.Font = Enum.Font.GothamBold
    Label.TextXAlignment = Enum.TextXAlignment.Left
    Label.TextYAlignment = Enum.TextYAlignment.Center
    Label.Parent = MainFrame
    return Label
end

-- ═══════════════════════════════════════════════════════════════
-- 8. CHECKBOX COMPONENT
-- ═══════════════════════════════════════════════════════════════

local function CreateCheckbox(name, label, xPosition, yPosition, parent)
    local Container = Instance.new("Frame")
    Container.Name = name
    Container.Size = UDim2.new(0.45, -10, 0, 28)
    Container.Position = UDim2.new(xPosition, 0, 0, yPosition)
    Container.BackgroundTransparency = 1
    Container.Parent = parent
    
    -- Checkbox BG
    local CheckboxBG = Instance.new("Frame")
    CheckboxBG.Name = "CheckboxBG"
    CheckboxBG.Size = UDim2.new(0, 18, 0, 18)
    CheckboxBG.Position = UDim2.new(0, 0, 0.5, -9)
    CheckboxBG.BackgroundColor3 = COLORS.CheckboxBG
    CheckboxBG.Parent = Container
    
    local CheckboxCorner = Instance.new("UICorner")
    CheckboxCorner.CornerRadius = UDim.new(0, 4)
    CheckboxCorner.Parent = CheckboxBG
    
    -- Check Icon (invisível inicialmente)
    local CheckIcon = Instance.new("TextLabel")
    CheckIcon.Name = "CheckIcon"
    CheckIcon.Size = UDim2.new(1, 0, 1, 0)
    CheckIcon.BackgroundTransparency = 1
    CheckIcon.Text = "✓"
    CheckIcon.TextColor3 = COLORS.Background
    CheckIcon.TextSize = 14
    CheckIcon.Font = Enum.Font.GothamBold
    CheckIcon.TextYAlignment = Enum.TextYAlignment.Center
    CheckIcon.TextXAlignment = Enum.TextXAlignment.Center
    CheckIcon.Visible = false
    CheckIcon.Parent = CheckboxBG
    
    -- Label
    local Label = Instance.new("TextLabel")
    Label.Name = "Label"
    Label.Size = UDim2.new(1, -24, 1, 0)
    Label.Position = UDim2.new(0, 24, 0, 0)
    Label.BackgroundTransparency = 1
    Label.Text = label
    Label.TextColor3 = COLORS.Text
    Label.TextSize = 13
    Label.Font = Enum.Font.Gotham
    Label.TextXAlignment = Enum.TextXAlignment.Left
    Label.TextYAlignment = Enum.TextYAlignment.Center
    Label.Parent = Container
    
    -- Estado
    local checked = false
    
    -- Função para alternar
    local function Toggle()
        checked = not checked
        if checked then
            TweenService:Create(CheckboxBG, TweenInfo.new(0.15), {
                BackgroundColor3 = COLORS.CheckboxChecked
            }):Play()
            CheckIcon.Visible = true
        else
            TweenService:Create(CheckboxBG, TweenInfo.new(0.15), {
                BackgroundColor3 = COLORS.CheckboxBG
            }):Play()
            CheckIcon.Visible = false
        end
    end
    
    -- Eventos
    Container.MouseButton1Click:Connect(Toggle)
    CheckboxBG.MouseButton1Click:Connect(Toggle)
    Label.MouseButton1Click:Connect(Toggle)
    
    -- Hover
    Container.MouseEnter:Connect(function()
        TweenService:Create(Label, TweenInfo.new(0.15), {
            TextColor3 = COLORS.Accent
        }):Play()
    end)
    
    Container.MouseLeave:Connect(function()
        TweenService:Create(Label, TweenInfo.new(0.15), {
            TextColor3 = COLORS.Text
        }):Play()
    end)
    
    return Container, Toggle
end

-- ═══════════════════════════════════════════════════════════════
-- 9. SEÇÃO BASE
-- ═══════════════════════════════════════════════════════════════

local baseY = 160

-- Divisor
CreateDivider(baseY)

-- Título
CreateSectionTitle("──────── Base ────────", baseY + 8)

-- Checkboxes da Base (2 colunas)
local baseItems = {
    {name = "ClaimCash", label = "Claim Cash"},
    {name = "ClaimParts", label = "Claim Parts"},
    {name = "BuyUpgrades", label = "Buy Upgrades"},
    {name = "ClaimContainers", label = "Claim Containers"},
    {name = "OpenContainers", label = "Open Containers"},
    {name = "ClaimQuests", label = "Claim Quests"},
    {name = "DeliverJunk", label = "Deliver Junk"},
}

for i, item in ipairs(baseItems) do
    local col = (i % 2 == 1) and 0 or 0.5
    local row = math.floor((i - 1) / 2)
    local yPos = baseY + 35 + (row * 30)
    CreateCheckbox(item.name, item.label, col, yPos, MainFrame)
end

-- ═══════════════════════════════════════════════════════════════
-- 10. SEÇÃO CARS
-- ═══════════════════════════════════════════════════════════════

local carsY = baseY + 155

-- Divisor
CreateDivider(carsY)

-- Título
CreateSectionTitle("──────── Cars ────────", carsY + 8)

-- Checkboxes da Cars
local carsItems = {
    {name = "FixCars", label = "Fix Cars"},
    {name = "SellFixedCars", label = "Sell Fixed Cars"},
    {name = "UpdateStands", label = "Update Stands"},
    {name = "BuyFixSellCheapest", label = "Buy, Fix, Sell Cheapest"},
}

for i, item in ipairs(carsItems) do
    local col = (i % 2 == 1) and 0 or 0.5
    local row = math.floor((i - 1) / 2)
    local yPos = carsY + 35 + (row * 30)
    CreateCheckbox(item.name, item.label, col, yPos, MainFrame)
end

-- ═══════════════════════════════════════════════════════════════
-- 11. SEÇÃO REWARDS
-- ═══════════════════════════════════════════════════════════════

local rewardsY = carsY + 100

-- Divisor
CreateDivider(rewardsY)

-- Título
CreateSectionTitle("──────── Rewards ────────", rewardsY + 8)

-- Checkboxes da Rewards
local rewardsItems = {
    {name = "ClaimPlaytimeRewards", label = "Claim Playtime Rewards"},
    {name = "ClaimDailyRewards", label = "Claim Daily Rewards"},
}

for i, item in ipairs(rewardsItems) do
    local col = (i % 2 == 1) and 0 or 0.5
    local row = math.floor((i - 1) / 2)
    local yPos = rewardsY + 35 + (row * 30)
    CreateCheckbox(item.name, item.label, col, yPos, MainFrame)
end

-- ═══════════════════════════════════════════════════════════════
-- 12. FUNCIONALIDADE ARRASTÁVEL (DRAGGABLE)
-- ═══════════════════════════════════════════════════════════════

local dragging = false
local dragStart = Vector2.new()
local startPos = UDim2.new()

TitleBar.MouseButton1Down:Connect(function(input)
    dragging = true
    dragStart = input.Position
    startPos = MainFrame.Position
    TitleBar.BackgroundTransparency = 0.1
end)

UserInputService.InputEnded:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseButton1 then
        if dragging then
            dragging = false
            TitleBar.BackgroundTransparency = 0
        end
    end
end)

UserInputService.InputChanged:Connect(function(input)
    if dragging and input.UserInputType == Enum.UserInputType.MouseMovement then
        local delta = input.Position - dragStart
        MainFrame.Position = UDim2.new(
            startPos.X.Scale,
            startPos.X.Offset + delta.X,
            startPos.Y.Scale,
            startPos.Y.Offset + delta.Y
        )
    end
end)

-- ═══════════════════════════════════════════════════════════════
-- 13. ANIMAÇÃO DE ENTRADA
-- ═══════════════════════════════════════════════════════════════

MainFrame.Size = UDim2.new(0, 420, 0, 0)
MainFrame.Position = UDim2.new(0.5, -210, 0.5, 0)

task.wait(0.1)

TweenService:Create(MainFrame, TweenInfo.new(0.4, Enum.EasingStyle.Back, Enum.EasingDirection.Out), {
    Size = UDim2.new(0, 420, 0, 580),
    Position = UDim2.new(0.5, -210, 0.5, -290)
}):Play()

-- ═══════════════════════════════════════════════════════════════
-- 14. EXPORTAÇÃO DE FUNÇÕES (Para uso externo)
-- ═══════════════════════════════════════════════════════════════

local CarFlipperAPI = {
    GetStatus = function()
        return StatusLabel.Text
    end,
    SetStatus = function(text)
        StatusLabel.Text = text
    end,
    GetCheckboxState = function(name)
        -- Implementar se necessário
    end,
    ToggleCheckbox = function(name)
        -- Implementar se necessário
    end,
    GetSearchText = function()
        return SearchTextBox.Text
    end,
    SetSearchText = function(text)
        SearchTextBox.Text = text
    end,
    SetTab = function(tab)
        if tab == "Auto" or tab == "RunOnce" then
            UpdateTabs(tab)
        end
    end,
    GetActiveTab = function()
        return ActiveTab
    end,
    Close = function()
        CloseButton.MouseButton1Click:Fire()
    end,
    Show = function()
        ScreenGui.Enabled = true
        MainFrame.Size = UDim2.new(0, 420, 0, 0)
        MainFrame.Position = UDim2.new(0.5, -210, 0.5, 0)
        task.wait(0.1)
        TweenService:Create(MainFrame, TweenInfo.new(0.4, Enum.EasingStyle.Back, Enum.EasingDirection.Out), {
            Size = UDim2.new(0, 420, 0, 580),
            Position = UDim2.new(0.5, -210, 0.5, -290)
        }):Play()
    end
}

-- Armazenar no _G para acesso global
_G.CarFlipper = CarFlipperAPI

print("✓ Car Flipper - GomezXitado carregado com sucesso!")

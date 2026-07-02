--[[
    ═══════════════════════════════════════════════════════════════
    CAR FLIPPER - GOMEZXITADO
    Interface com Layout Horizontal (Base Retangular)
    Design Moderno com Abas e Checkboxes
    ═══════════════════════════════════════════════════════════════
--]]

-- ═══════════════════════════════════════════════════════════════
-- 1. CONFIGURAÇÕES E IMPORTAÇÕES
-- ═══════════════════════════════════════════════════════════════

local Players = game:GetService("Players")
local LocalPlayer = Players.LocalPlayer
local TweenService = game:GetService("TweenService")
local UserInputService = game:GetService("UserInputService")

-- ═══════════════════════════════════════════════════════════════
-- 2. TEMA ESCURO (DARK THEME)
-- ═══════════════════════════════════════════════════════════════

local Theme = {
    Background = Color3.fromRGB(18, 18, 24),
    Surface = Color3.fromRGB(28, 28, 36),
    SurfaceLight = Color3.fromRGB(38, 38, 48),
    SurfaceLighter = Color3.fromRGB(48, 48, 58),
    
    TextPrimary = Color3.fromRGB(235, 235, 245),
    TextSecondary = Color3.fromRGB(175, 175, 190),
    TextMuted = Color3.fromRGB(120, 120, 140),
    
    Accent = Color3.fromRGB(255, 200, 50),
    AccentHover = Color3.fromRGB(255, 215, 75),
    
    Border = Color3.fromRGB(55, 55, 70),
    Divider = Color3.fromRGB(42, 42, 55),
    Shadow = Color3.fromRGB(0, 0, 0),
    
    CheckboxBG = Color3.fromRGB(45, 45, 58),
    CheckboxChecked = Color3.fromRGB(255, 200, 50),
    InputBG = Color3.fromRGB(35, 35, 45),
    
    Danger = Color3.fromRGB(220, 60, 60),
}

-- ═══════════════════════════════════════════════════════════════
-- 3. CRIAÇÃO DA INTERFACE
-- ═══════════════════════════════════════════════════════════════

-- 3.1 ScreenGui
local MainGui = Instance.new("ScreenGui")
MainGui.Name = "CarFlipperGUI"
MainGui.Parent = LocalPlayer:WaitForChild("PlayerGui")
MainGui.ResetOnSpawn = false

-- 3.2 MainFrame (Janela Principal - Horizontal)
local MainFrame = Instance.new("Frame")
MainFrame.Name = "MainFrame"
MainFrame.Size = UDim2.new(0, 900, 0, 520)  -- Largura maior, altura menor (horizontal)
MainFrame.Position = UDim2.new(0.5, -450, 0.5, -260)
MainFrame.BackgroundColor3 = Theme.Background
MainFrame.BorderSizePixel = 0
MainFrame.ClipsDescendants = true
MainFrame.Parent = MainGui

-- Corner da MainFrame
local MainCorner = Instance.new("UICorner")
MainCorner.CornerRadius = UDim.new(0, 14)
MainCorner.Parent = MainFrame

-- Stroke da MainFrame
local MainStroke = Instance.new("UIStroke")
MainStroke.Color = Theme.Border
MainStroke.Thickness = 1.5
MainStroke.Parent = MainFrame

-- 3.3 Sombra
local Shadow = Instance.new("Frame")
Shadow.Name = "Shadow"
Shadow.Size = UDim2.new(1, 20, 1, 20)
Shadow.Position = UDim2.new(0, -10, 0, -10)
Shadow.BackgroundColor3 = Theme.Shadow
Shadow.BackgroundTransparency = 0.5
Shadow.BorderSizePixel = 0
Shadow.Parent = MainFrame

local ShadowCorner = Instance.new("UICorner")
ShadowCorner.CornerRadius = UDim.new(0, 18)
ShadowCorner.Parent = Shadow

-- ═══════════════════════════════════════════════════════════════
-- 4. BARRA DE TÍTULO
-- ═══════════════════════════════════════════════════════════════

local TitleBar = Instance.new("Frame")
TitleBar.Name = "TitleBar"
TitleBar.Size = UDim2.new(1, 0, 0, 48)
TitleBar.BackgroundColor3 = Theme.SurfaceLight
TitleBar.BorderSizePixel = 0
TitleBar.Parent = MainFrame

local TitleCorner = Instance.new("UICorner")
TitleCorner.CornerRadius = UDim.new(0, 14)
TitleCorner.Parent = TitleBar

-- Título
local TitleText = Instance.new("TextLabel")
TitleText.Name = "Title"
TitleText.Size = UDim2.new(1, -60, 1, 0)
TitleText.Position = UDim2.new(0, 18, 0, 0)
TitleText.BackgroundTransparency = 1
TitleText.Text = "Car Flipper - GomezXitado"
TitleText.TextColor3 = Theme.TextPrimary
TitleText.TextSize = 17
TitleText.Font = Enum.Font.GothamSemibold
TitleText.TextXAlignment = Enum.TextXAlignment.Left
TitleText.TextYAlignment = Enum.TextYAlignment.Center
TitleText.Parent = TitleBar

-- Botão Fechar
local CloseBtn = Instance.new("TextButton")
CloseBtn.Name = "CloseBtn"
CloseBtn.Size = UDim2.new(0, 34, 0, 34)
CloseBtn.Position = UDim2.new(1, -44, 0.5, -17)
CloseBtn.BackgroundColor3 = Theme.SurfaceLight
CloseBtn.BackgroundTransparency = 0
CloseBtn.Text = "✕"
CloseBtn.TextColor3 = Theme.TextSecondary
CloseBtn.TextSize = 18
CloseBtn.Font = Enum.Font.Gotham
CloseBtn.BorderSizePixel = 0
CloseBtn.Parent = TitleBar

local CloseCorner = Instance.new("UICorner")
CloseCorner.CornerRadius = UDim.new(1, 0)
CloseCorner.Parent = CloseBtn

CloseBtn.MouseEnter:Connect(function()
    TweenService:Create(CloseBtn, TweenInfo.new(0.15), {
        BackgroundColor3 = Theme.Danger,
        TextColor3 = Color3.fromRGB(255, 255, 255)
    }):Play()
end)

CloseBtn.MouseLeave:Connect(function()
    TweenService:Create(CloseBtn, TweenInfo.new(0.15), {
        BackgroundColor3 = Theme.SurfaceLight,
        TextColor3 = Theme.TextSecondary
    }):Play()
end)

CloseBtn.MouseButton1Click:Connect(function()
    TweenService:Create(MainFrame, TweenInfo.new(0.3, Enum.EasingStyle.Quint), {
        Size = UDim2.new(0, 900, 0, 0),
        Position = UDim2.new(0.5, -450, 0.5, 0)
    }):Play()
    task.wait(0.3)
    MainGui.Enabled = false
end)

-- ═══════════════════════════════════════════════════════════════
-- 5. ABAS (TABS)
-- ═══════════════════════════════════════════════════════════════

local TabContainer = Instance.new("Frame")
TabContainer.Name = "TabContainer"
TabContainer.Size = UDim2.new(1, -20, 0, 42)
TabContainer.Position = UDim2.new(0, 10, 0, 54)
TabContainer.BackgroundTransparency = 1
TabContainer.Parent = MainFrame

local Tabs = {}
local ActiveTab = "Auto"

function CreateTab(name, label)
    local tab = Instance.new("TextButton")
    tab.Name = name .. "Tab"
    tab.Size = UDim2.new(0.5, -4, 1, -4)
    tab.Position = (name == "Auto") and UDim2.new(0, 0, 0, 2) or UDim2.new(0.5, 4, 0, 2)
    tab.BackgroundColor3 = (name == "Auto") and Theme.Accent or Theme.Surface
    tab.Text = label
    tab.TextColor3 = (name == "Auto") and Theme.Background or Theme.TextSecondary
    tab.TextSize = 14
    tab.Font = Enum.Font.GothamMedium
    tab.BorderSizePixel = 0
    tab.Parent = TabContainer
    
    local tabCorner = Instance.new("UICorner")
    tabCorner.CornerRadius = UDim.new(0, 8)
    tabCorner.Parent = tab
    
    tab.MouseButton1Click:Connect(function()
        ActiveTab = name
        for tabName, tabBtn in pairs(Tabs) do
            local isActive = (tabName == name)
            TweenService:Create(tabBtn, TweenInfo.new(0.2), {
                BackgroundColor3 = isActive and Theme.Accent or Theme.Surface,
                TextColor3 = isActive and Theme.Background or Theme.TextSecondary
            }):Play()
        end
    end)
    
    Tabs[name] = tab
    return tab
end

CreateTab("Auto", "Auto")
CreateTab("RunOnce", "Run Once")

-- ═══════════════════════════════════════════════════════════════
-- 6. CAMPO DE BUSCA (TEXTBOX)
-- ═══════════════════════════════════════════════════════════════

local SearchContainer = Instance.new("Frame")
SearchContainer.Name = "SearchContainer"
SearchContainer.Size = UDim2.new(1, -20, 0, 38)
SearchContainer.Position = UDim2.new(0, 10, 0, 102)
SearchContainer.BackgroundTransparency = 1
SearchContainer.Parent = MainFrame

-- Input
local SearchInput = Instance.new("TextBox")
SearchInput.Name = "SearchInput"
SearchInput.Size = UDim2.new(0.78, -6, 1, 0)
SearchInput.Position = UDim2.new(0, 0, 0, 0)
SearchInput.BackgroundColor3 = Theme.InputBG
SearchInput.BorderSizePixel = 0
SearchInput.Text = ""
SearchInput.PlaceholderText = "Digite algo..."
SearchInput.PlaceholderColor3 = Theme.TextMuted
SearchInput.TextColor3 = Theme.TextPrimary
SearchInput.TextSize = 13
SearchInput.Font = Enum.Font.Gotham
SearchInput.ClearTextOnFocus = false
SearchInput.Parent = SearchContainer

local SearchCorner = Instance.new("UICorner")
SearchCorner.CornerRadius = UDim.new(0, 8)
SearchCorner.Parent = SearchInput

local SearchStroke = Instance.new("UIStroke")
SearchStroke.Color = Theme.Border
SearchStroke.Thickness = 1
SearchStroke.Parent = SearchInput

-- Botão Enviar
local SendBtn = Instance.new("TextButton")
SendBtn.Name = "SendBtn"
SendBtn.Size = UDim2.new(0.22, -6, 1, 0)
SendBtn.Position = UDim2.new(0.78, 10, 0, 0)
SendBtn.BackgroundColor3 = Theme.Accent
SendBtn.Text = "Enviar"
SendBtn.TextColor3 = Theme.Background
SendBtn.TextSize = 13
SendBtn.Font = Enum.Font.GothamMedium
SendBtn.BorderSizePixel = 0
SendBtn.Parent = SearchContainer

local SendCorner = Instance.new("UICorner")
SendCorner.CornerRadius = UDim.new(0, 8)
SendCorner.Parent = SendBtn

SendBtn.MouseEnter:Connect(function()
    TweenService:Create(SendBtn, TweenInfo.new(0.15), {
        BackgroundColor3 = Theme.AccentHover
    }):Play()
end)

SendBtn.MouseLeave:Connect(function()
    TweenService:Create(SendBtn, TweenInfo.new(0.15), {
        BackgroundColor3 = Theme.Accent
    }):Play()
end)

SendBtn.MouseButton1Click:Connect(function()
    if SearchInput.Text ~= "" then
        print("[Car Flipper] Busca:", SearchInput.Text)
        SearchInput.Text = ""
    end
end)

-- ═══════════════════════════════════════════════════════════════
-- 7. STATUS
-- ═══════════════════════════════════════════════════════════════

local StatusLabel = Instance.new("TextLabel")
StatusLabel.Name = "StatusLabel"
StatusLabel.Size = UDim2.new(1, -20, 0, 24)
StatusLabel.Position = UDim2.new(0, 12, 0, 148)
StatusLabel.BackgroundTransparency = 1
StatusLabel.Text = "⏳ [Car Flipper] Idle"
StatusLabel.TextColor3 = Theme.TextSecondary
StatusLabel.TextSize = 13
StatusLabel.Font = Enum.Font.GothamMedium
StatusLabel.TextXAlignment = Enum.TextXAlignment.Left
StatusLabel.TextYAlignment = Enum.TextYAlignment.Center
StatusLabel.Parent = MainFrame

-- ═══════════════════════════════════════════════════════════════
-- 8. CONTAINER DE CONTEÚDO (HORIZONTAL - LAYOUT EM GRID)
-- ═══════════════════════════════════════════════════════════════

-- Container principal para as seções (layout horizontal)
local ContentContainer = Instance.new("Frame")
ContentContainer.Name = "ContentContainer"
ContentContainer.Size = UDim2.new(1, -20, 1, -190)
ContentContainer.Position = UDim2.new(0, 10, 0, 180)
ContentContainer.BackgroundTransparency = 1
ContentContainer.ClipsDescendants = true
ContentContainer.Parent = MainFrame

-- Canvas para scroll (se necessário)
local Canvas = Instance.new("Frame")
Canvas.Name = "Canvas"
Canvas.Size = UDim2.new(1, 0, 0, 0)
Canvas.BackgroundTransparency = 1
Canvas.Parent = ContentContainer

-- ═══════════════════════════════════════════════════════════════
-- 9. SISTEMA DE CHECKBOX PERSONALIZADO
-- ═══════════════════════════════════════════════════════════════

local Checkboxes = {}

function CreateCheckbox(parent, name, label, xPos, yPos)
    local container = Instance.new("Frame")
    container.Name = name
    container.Size = UDim2.new(0, 160, 0, 28)  -- Largura fixa para horizontal
    container.Position = UDim2.new(xPos, 0, 0, yPos)
    container.BackgroundTransparency = 1
    container.Parent = parent
    
    -- Checkbox BG
    local checkBG = Instance.new("Frame")
    checkBG.Name = "CheckBG"
    checkBG.Size = UDim2.new(0, 20, 0, 20)
    checkBG.Position = UDim2.new(0, 0, 0.5, -10)
    checkBG.BackgroundColor3 = Theme.CheckboxBG
    checkBG.BorderSizePixel = 0
    checkBG.Parent = container
    
    local checkCorner = Instance.new("UICorner")
    checkCorner.CornerRadius = UDim.new(0, 5)
    checkCorner.Parent = checkBG
    
    local checkStroke = Instance.new("UIStroke")
    checkStroke.Color = Theme.Border
    checkStroke.Thickness = 1
    checkStroke.Parent = checkBG
    
    -- Ícone de Check
    local checkIcon = Instance.new("TextLabel")
    checkIcon.Name = "CheckIcon"
    checkIcon.Size = UDim2.new(1, 0, 1, 0)
    checkIcon.BackgroundTransparency = 1
    checkIcon.Text = "✓"
    checkIcon.TextColor3 = Theme.Background
    checkIcon.TextSize = 16
    checkIcon.Font = Enum.Font.GothamBold
    checkIcon.TextXAlignment = Enum.TextXAlignment.Center
    checkIcon.TextYAlignment = Enum.TextYAlignment.Center
    checkIcon.Visible = false
    checkIcon.Parent = checkBG
    
    -- Label
    local label = Instance.new("TextLabel")
    label.Name = "Label"
    label.Size = UDim2.new(1, -26, 1, 0)
    label.Position = UDim2.new(0, 26, 0, 0)
    label.BackgroundTransparency = 1
    label.Text = label
    label.TextColor3 = Theme.TextPrimary
    label.TextSize = 13
    label.Font = Enum.Font.Gotham
    label.TextXAlignment = Enum.TextXAlignment.Left
    label.TextYAlignment = Enum.TextYAlignment.Center
    label.Parent = container
    
    -- Estado
    local state = {
        checked = false,
        container = container,
        checkBG = checkBG,
        checkIcon = checkIcon,
        label = label
    }
    
    function state:Toggle()
        self.checked = not self.checked
        if self.checked then
            TweenService:Create(self.checkBG, TweenInfo.new(0.15), {
                BackgroundColor3 = Theme.CheckboxChecked
            }):Play()
            self.checkIcon.Visible = true
        else
            TweenService:Create(self.checkBG, TweenInfo.new(0.15), {
                BackgroundColor3 = Theme.CheckboxBG
            }):Play()
            self.checkIcon.Visible = false
        end
    end
    
    -- Eventos de clique
    container.InputBegan:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1 then
            state:Toggle()
        end
    end)
    
    -- Hover
    container.MouseEnter:Connect(function()
        TweenService:Create(label, TweenInfo.new(0.15), {
            TextColor3 = Theme.Accent
        }):Play()
    end)
    
    container.MouseLeave:Connect(function()
        if not state.checked then
            TweenService:Create(label, TweenInfo.new(0.15), {
                TextColor3 = Theme.TextPrimary
            }):Play()
        end
    end)
    
    Checkboxes[name] = state
    return state
end

-- ═══════════════════════════════════════════════════════════════
-- 10. FUNÇÃO PARA CRIAR SEÇÕES (HORIZONTAL)
-- ═══════════════════════════════════════════════════════════════

local sectionY = 0
local sectionSpacing = 20

function CreateSection(title, items)
    -- Container da seção (retângulo horizontal)
    local sectionContainer = Instance.new("Frame")
    sectionContainer.Name = title:gsub("─", ""):gsub(" ", "") .. "Section"
    sectionContainer.Size = UDim2.new(0, 280, 0, 0)
    sectionContainer.Position = UDim2.new(0, 0, 0, sectionY)
    sectionContainer.BackgroundColor3 = Theme.Surface
    sectionContainer.BackgroundTransparency = 0
    sectionContainer.BorderSizePixel = 0
    sectionContainer.Parent = Canvas
    
    local sectionCorner = Instance.new("UICorner")
    sectionCorner.CornerRadius = UDim.new(0, 10)
    sectionCorner.Parent = sectionContainer
    
    local sectionStroke = Instance.new("UIStroke")
    sectionStroke.Color = Theme.Border
    sectionStroke.Thickness = 1
    sectionStroke.Parent = sectionContainer
    
    -- Título da seção
    local sectionTitle = Instance.new("TextLabel")
    sectionTitle.Name = "Title"
    sectionTitle.Size = UDim2.new(1, -16, 0, 32)
    sectionTitle.Position = UDim2.new(0, 8, 0, 4)
    sectionTitle.BackgroundTransparency = 1
    sectionTitle.Text = title
    sectionTitle.TextColor3 = Theme.TextMuted
    sectionTitle.TextSize = 12
    sectionTitle.Font = Enum.Font.GothamBold
    sectionTitle.TextXAlignment = Enum.TextXAlignment.Left
    sectionTitle.TextYAlignment = Enum.TextYAlignment.Center
    sectionTitle.Parent = sectionContainer
    
    -- Divider
    local divider = Instance.new("Frame")
    divider.Name = "Divider"
    divider.Size = UDim2.new(0.9, 0, 0, 1)
    divider.Position = UDim2.new(0.05, 0, 0, 40)
    divider.BackgroundColor3 = Theme.Divider
    divider.BorderSizePixel = 0
    divider.Parent = sectionContainer
    
    -- Criar checkboxes em grid horizontal
    local startX = 8
    local startY = 48
    local itemsPerRow = 1
    local currentX = startX
    local currentY = startY
    local maxWidth = 0
    
    for i, item in ipairs(items) do
        -- Calcular posição (vertical em colunas)
        local col = math.floor((i - 1) / 4)  -- 4 itens por coluna
        local row = (i - 1) % 4
        
        local xPos = currentX + (col * 130)
        local yPos = currentY + (row * 30)
        
        CreateCheckbox(sectionContainer, item.name, item.label, 0, yPos)
        maxWidth = math.max(maxWidth, xPos + 160)
    end
    
    -- Ajustar tamanho do container baseado no conteúdo
    local totalHeight = startY + (math.min(#items, 4) * 30) + 16
    sectionContainer.Size = UDim2.new(0, math.max(maxWidth + 16, 160), 0, totalHeight)
    
    -- Atualizar Y para próxima seção
    sectionY = sectionY + totalHeight + sectionSpacing
    
    return sectionContainer
end

-- ═══════════════════════════════════════════════════════════════
-- 11. DEFINIÇÃO DOS DADOS DAS SEÇÕES
-- ═══════════════════════════════════════════════════════════════

local SectionsData = {
    {
        title = "──────── Base ────────",
        items = {
            {name = "ClaimCash", label = "Claim Cash"},
            {name = "ClaimParts", label = "Claim Parts"},
            {name = "BuyUpgrades", label = "Buy Upgrades"},
            {name = "ClaimContainers", label = "Claim Containers"},
            {name = "OpenContainers", label = "Open Containers"},
            {name = "ClaimQuests", label = "Claim Quests"},
            {name = "DeliverJunk", label = "Deliver Junk"},
        }
    },
    {
        title = "──────── Cars ────────",
        items = {
            {name = "FixCars", label = "Fix Cars"},
            {name = "SellFixedCars", label = "Sell Fixed Cars"},
            {name = "UpdateStands", label = "Update Stands"},
            {name = "BuyFixSellCheapest", label = "Buy, Fix, Sell Cheapest"},
        }
    },
    {
        title = "──────── Rewards ────────",
        items = {
            {name = "ClaimPlaytimeRewards", label = "Claim Playtime Rewards"},
            {name = "ClaimDailyRewards", label = "Claim Daily Rewards"},
        }
    }
}

-- ═══════════════════════════════════════════════════════════════
-- 12. CONSTRUÇÃO DAS SEÇÕES (LAYOUT HORIZONTAL)
-- ═══════════════════════════════════════════════════════════════

-- Posicionar as seções horizontalmente (uma ao lado da outra)
local totalSections = #SectionsData
local sectionWidth = 280  -- Largura de cada seção
local spacing = 20
local totalWidth = (totalSections * sectionWidth) + ((totalSections - 1) * spacing)

-- Ajustar Canvas para acomodar todas as seções horizontalmente
local canvasWidth = math.max(totalWidth, 860)  -- Mínimo 860px
Canvas.Size = UDim2.new(0, canvasWidth, 0, 340)

-- Criar cada seção com posição horizontal
for i, sectionData in ipairs(SectionsData) do
    local xPos = (i - 1) * (sectionWidth + spacing)
    
    -- Container da seção
    local sectionContainer = Instance.new("Frame")
    sectionContainer.Name = sectionData.title:gsub("─", ""):gsub(" ", "") .. "Section"
    sectionContainer.Size = UDim2.new(0, sectionWidth, 0, 0)
    sectionContainer.Position = UDim2.new(0, xPos, 0, 0)
    sectionContainer.BackgroundColor3 = Theme.Surface
    sectionContainer.BackgroundTransparency = 0
    sectionContainer.BorderSizePixel = 0
    sectionContainer.Parent = Canvas
    
    local sectionCorner = Instance.new("UICorner")
    sectionCorner.CornerRadius = UDim.new(0, 10)
    sectionCorner.Parent = sectionContainer
    
    local sectionStroke = Instance.new("UIStroke")
    sectionStroke.Color = Theme.Border
    sectionStroke.Thickness = 1
    sectionStroke.Parent = sectionContainer
    
    -- Título da seção
    local sectionTitle = Instance.new("TextLabel")
    sectionTitle.Name = "Title"
    sectionTitle.Size = UDim2.new(1, -16, 0, 32)
    sectionTitle.Position = UDim2.new(0, 8, 0, 4)
    sectionTitle.BackgroundTransparency = 1
    sectionTitle.Text = sectionData.title
    sectionTitle.TextColor3 = Theme.TextMuted
    sectionTitle.TextSize = 12
    sectionTitle.Font = Enum.Font.GothamBold
    sectionTitle.TextXAlignment = Enum.TextXAlignment.Left
    sectionTitle.TextYAlignment = Enum.TextYAlignment.Center
    sectionTitle.Parent = sectionContainer
    
    -- Divider
    local divider = Instance.new("Frame")
    divider.Name = "Divider"
    divider.Size = UDim2.new(0.9, 0, 0, 1)
    divider.Position = UDim2.new(0.05, 0, 0, 40)
    divider.BackgroundColor3 = Theme.Divider
    divider.BorderSizePixel = 0
    divider.Parent = sectionContainer
    
    -- Criar checkboxes em layout vertical
    local startY = 48
    local itemsPerColumn = 6  -- Máximo de itens por coluna
    
    for i, item in ipairs(sectionData.items) do
        local row = i - 1
        local yPos = startY + (row * 30)
        
        CreateCheckbox(sectionContainer, item.name, item.label, 0, yPos)
    end
    
    -- Ajustar altura do container
    local totalHeight = startY + (#sectionData.items * 30) + 16
    sectionContainer.Size = UDim2.new(0, sectionWidth, 0, totalHeight)
end

-- ═══════════════════════════════════════════════════════════════
-- 13. SISTEMA DE ARRASTAR
-- ═══════════════════════════════════════════════════════════════

local Dragging = false
local DragStart = Vector2.new()
local StartPos = UDim2.new()

TitleBar.InputBegan:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseButton1 then
        Dragging = true
        DragStart = input.Position
        StartPos = MainFrame.Position
        TweenService:Create(TitleBar, TweenInfo.new(0.15), {
            BackgroundTransparency = 0.1
        }):Play()
    end
end)

UserInputService.InputEnded:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseButton1 and Dragging then
        Dragging = false
        TweenService:Create(TitleBar, TweenInfo.new(0.15), {
            BackgroundTransparency = 0
        }):Play()
    end
end)

UserInputService.InputChanged:Connect(function(input)
    if Dragging and input.UserInputType == Enum.UserInputType.MouseMovement then
        local delta = input.Position - DragStart
        MainFrame.Position = UDim2.new(
            StartPos.X.Scale,
            StartPos.X.Offset + delta.X,
            StartPos.Y.Scale,
            StartPos.Y.Offset + delta.Y
        )
    end
end)

-- ═══════════════════════════════════════════════════════════════
-- 14. ANIMAÇÃO DE ENTRADA
-- ═══════════════════════════════════════════════════════════════

MainFrame.Size = UDim2.new(0, 900, 0, 0)
MainFrame.Position = UDim2.new(0.5, -450, 0.5, 0)

task.wait(0.1)

TweenService:Create(MainFrame, TweenInfo.new(0.5, Enum.EasingStyle.Back, Enum.EasingDirection.Out), {
    Size = UDim2.new(0, 900, 0, 520),
    Position = UDim2.new(0.5, -450, 0.5, -260)
}):Play()

-- ═══════════════════════════════════════════════════════════════
-- 15. API PÚBLICA
-- ═══════════════════════════════════════════════════════════════

local CarFlipperAPI = {
    GetStatus = function()
        return StatusLabel.Text
    end,
    SetStatus = function(text)
        StatusLabel.Text = "⏳ " .. text
    end,
    SetIdle = function()
        StatusLabel.Text = "⏳ [Car Flipper] Idle"
    end,
    
    GetCheckbox = function(name)
        return Checkboxes[name]
    end,
    ToggleCheckbox = function(name)
        local cb = Checkboxes[name]
        if cb then cb:Toggle() end
    end,
    IsChecked = function(name)
        local cb = Checkboxes[name]
        return cb and cb.checked or false
    end,
    GetAllStates = function()
        local states = {}
        for name, cb in pairs(Checkboxes) do
            states[name] = cb.checked
        end
        return states
    end,
    
    GetSearchText = function()
        return SearchInput.Text
    end,
    SetSearchText = function(text)
        SearchInput.Text = text
    end,
    ClearSearch = function()
        SearchInput.Text = ""
    end,
    
    GetActiveTab = function()
        return ActiveTab
    end,
    SetActiveTab = function(tab)
        if Tabs[tab] then
            Tabs[tab].MouseButton1Click:Fire()
        end
    end,
    
    Show = function()
        MainGui.Enabled = true
        MainFrame.Size = UDim2.new(0, 900, 0, 0)
        MainFrame.Position = UDim2.new(0.5, -450, 0.5, 0)
        task.wait(0.1)
        TweenService:Create(MainFrame, TweenInfo.new(0.5, Enum.EasingStyle.Back, Enum.EasingDirection.Out), {
            Size = UDim2.new(0, 900, 0, 520),
            Position = UDim2.new(0.5, -450, 0.5, -260)
        }):Play()
    end,
    Hide = function()
        TweenService:Create(MainFrame, TweenInfo.new(0.3, Enum.EasingStyle.Quint), {
            Size = UDim2.new(0, 900, 0, 0),
            Position = UDim2.new(0.5, -450, 0.5, 0)
        }):Play()
        task.wait(0.3)
        MainGui.Enabled = false
    end,
    Toggle = function()
        if MainGui.Enabled then
            CarFlipperAPI.Hide()
        else
            CarFlipperAPI.Show()
        end
    end,
}

_G.CarFlipper = CarFlipperAPI

-- ═══════════════════════════════════════════════════════════════
-- 16. FINALIZAÇÃO
-- ═══════════════════════════════════════════════════════════════

print("✅ Car Flipper - GomezXitado carregado com sucesso!")
print("📐 Layout Horizontal - Retangular")
print("📌 Use _G.CarFlipper para controlar a interface")

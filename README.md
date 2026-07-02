--[[
    ═══════════════════════════════════════════════════════════════
    CAR FLIPPER - GOMEZXITADO
    Interface Premium com Layout Horizontal
    BOTÃO PRETO NO CANTO SUPERIOR DIREITO
    ABRE AUTOMATICAMENTE AO EXECUTAR
    ═══════════════════════════════════════════════════════════════
--]]

-- ═══════════════════════════════════════════════════════════════
-- 1. CONFIGURAÇÕES E IMPORTAÇÕES
-- ═══════════════════════════════════════════════════════════════

local Players = game:GetService("Players")
local LocalPlayer = Players.LocalPlayer
local TweenService = game:GetService("TweenService")
local UserInputService = game:GetService("UserInputService")
local RunService = game:GetService("RunService")
local HttpService = game:GetService("HttpService")

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
    
    Danger = Color3.fromRGB(220, 60, 60),
    Gold = Color3.fromRGB(255, 200, 50),
}

-- ═══════════════════════════════════════════════════════════════
-- 3. SISTEMA DE RELÓGIO EM TEMPO REAL
-- ═══════════════════════════════════════════════════════════════

local ClockSystem = {
    currentTime = "",
    lastUpdate = 0,
    updateInterval = 1,
    isRunning = true,
}

function ClockSystem:GetCurrentDateTime()
    local success, result = pcall(function()
        local response = HttpService:GetAsync("https://worldtimeapi.org/api/timezone/America/Sao_Paulo")
        if response then
            local data = HttpService:JSONDecode(response)
            if data and data.datetime then
                local datetime = data.datetime
                local parts = {}
                for part in string.gmatch(datetime, "[^T]+") do
                    table.insert(parts, part)
                end
                
                if #parts >= 2 then
                    local datePart = parts[1]
                    local timePart = string.sub(parts[2], 1, 5)
                    local dateFormatted = string.gsub(datePart, "(%d+)-(%d+)-(%d+)", "%3/%2/%1")
                    return timePart .. " " .. dateFormatted
                end
            end
        end
    end)
    
    if not success or not result then
        local currentTime = os.time()
        local dateTable = os.date("*t", currentTime)
        local hour = string.format("%02d", dateTable.hour)
        local min = string.format("%02d", dateTable.min)
        local day = string.format("%02d", dateTable.day)
        local month = string.format("%02d", dateTable.month)
        local year = dateTable.year
        
        return hour .. ":" .. min .. " " .. day .. "/" .. month .. "/" .. year
    end
    
    return result
end

function ClockSystem:Update()
    if not self.isRunning then return end
    
    local currentTime = tick()
    if currentTime - self.lastUpdate >= self.updateInterval then
        local newTime = self:GetCurrentDateTime()
        if newTime ~= self.currentTime then
            self.currentTime = newTime
            return true
        end
        self.lastUpdate = currentTime
    end
    return false
end

-- ═══════════════════════════════════════════════════════════════
-- 4. BOTÃO FLUTUANTE (Toggle Button) - PRETO NO CANTO SUPERIOR DIREITO
-- ═══════════════════════════════════════════════════════════════

-- Criar o ScreenGui para o botão
local ToggleGui = Instance.new("ScreenGui")
ToggleGui.Name = "ToggleGUI"
ToggleGui.Parent = LocalPlayer:WaitForChild("PlayerGui")
ToggleGui.ResetOnSpawn = false
ToggleGui.ZIndexBehavior = Enum.ZIndexBehavior.Sibling

-- Botão flutuante
local ToggleButton = Instance.new("ImageButton")
ToggleButton.Name = "ToggleButton"
ToggleButton.Size = UDim2.new(0, 36, 0, 36)
ToggleButton.Position = UDim2.new(1, -52, 0, 8) -- Ao lado da engrenagem
ToggleButton.BackgroundColor3 = Color3.fromRGB(30, 30, 35) -- Preto acinzentado
ToggleButton.BackgroundTransparency = 0
ToggleButton.Image = "rbxassetid://6031091079" -- Ícone de engrenagem/menu
ToggleButton.ImageColor3 = Color3.fromRGB(200, 200, 210)
ToggleButton.BorderSizePixel = 0
ToggleButton.Parent = ToggleGui

-- Corner do botão
local ToggleCorner = Instance.new("UICorner")
ToggleCorner.CornerRadius = UDim.new(0, 8)
ToggleCorner.Parent = ToggleButton

-- Sombra do botão
local ToggleShadow = Instance.new("Frame")
ToggleShadow.Name = "Shadow"
ToggleShadow.Size = UDim2.new(1, 8, 1, 8)
ToggleShadow.Position = UDim2.new(0, -4, 0, -4)
ToggleShadow.BackgroundColor3 = Theme.Shadow
ToggleShadow.BackgroundTransparency = 0.4
ToggleShadow.BorderSizePixel = 0
ToggleShadow.Parent = ToggleButton

local ToggleShadowCorner = Instance.new("UICorner")
ToggleShadowCorner.CornerRadius = UDim.new(0, 10)
ToggleShadowCorner.Parent = ToggleShadow

-- Efeitos do botão
ToggleButton.MouseEnter:Connect(function()
    TweenService:Create(ToggleButton, TweenInfo.new(0.15), {
        Size = UDim2.new(0, 38, 0, 38),
        BackgroundColor3 = Color3.fromRGB(45, 45, 50)
    }):Play()
end)

ToggleButton.MouseLeave:Connect(function()
    TweenService:Create(ToggleButton, TweenInfo.new(0.15), {
        Size = UDim2.new(0, 36, 0, 36),
        BackgroundColor3 = Color3.fromRGB(30, 30, 35)
    }):Play()
end)

-- Tooltip ao passar o mouse
local Tooltip = Instance.new("TextLabel")
Tooltip.Name = "Tooltip"
Tooltip.Size = UDim2.new(0, 120, 0, 28)
Tooltip.Position = UDim2.new(0, -130, 0.5, -14)
Tooltip.BackgroundColor3 = Color3.fromRGB(25, 25, 30)
Tooltip.BackgroundTransparency = 0.9
Tooltip.Text = "Car Flipper"
Tooltip.TextColor3 = Color3.fromRGB(220, 220, 230)
Tooltip.TextSize = 12
Tooltip.Font = Enum.Font.GothamMedium
Tooltip.TextXAlignment = Enum.TextXAlignment.Center
Tooltip.TextYAlignment = Enum.TextYAlignment.Center
Tooltip.BorderSizePixel = 0
Tooltip.Visible = false
Tooltip.Parent = ToggleButton

local TooltipCorner = Instance.new("UICorner")
TooltipCorner.CornerRadius = UDim.new(0, 6)
TooltipCorner.Parent = Tooltip

ToggleButton.MouseEnter:Connect(function()
    Tooltip.Visible = true
    TweenService:Create(Tooltip, TweenInfo.new(0.2), {
        BackgroundTransparency = 0.85
    }):Play()
end)

ToggleButton.MouseLeave:Connect(function()
    TweenService:Create(Tooltip, TweenInfo.new(0.2), {
        BackgroundTransparency = 0.9
    }):Play()
    task.wait(0.2)
    Tooltip.Visible = false
end)

-- ═══════════════════════════════════════════════════════════════
-- 5. CRIAÇÃO DA INTERFACE PRINCIPAL
-- ═══════════════════════════════════════════════════════════════

-- 5.1 ScreenGui Principal
local MainGui = Instance.new("ScreenGui")
MainGui.Name = "CarFlipperGUI"
MainGui.Parent = LocalPlayer:WaitForChild("PlayerGui")
MainGui.ResetOnSpawn = false

-- 5.2 MainFrame
local MainFrame = Instance.new("Frame")
MainFrame.Name = "MainFrame"
MainFrame.Size = UDim2.new(0, 920, 0, 560)
MainFrame.Position = UDim2.new(0.5, -460, 0.5, -280)
MainFrame.BackgroundColor3 = Theme.Background
MainFrame.BorderSizePixel = 0
MainFrame.ClipsDescendants = true
MainFrame.Parent = MainGui
MainFrame.Visible = true -- Começa visível (abre automaticamente)

local MainCorner = Instance.new("UICorner")
MainCorner.CornerRadius = UDim.new(0, 14)
MainCorner.Parent = MainFrame

local MainStroke = Instance.new("UIStroke")
MainStroke.Color = Theme.Border
MainStroke.Thickness = 1.5
MainStroke.Parent = MainFrame

-- Sombra do MainFrame
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
-- 6. BARRA DE TÍTULO (SEM BOTÃO FECHAR)
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
TitleText.Size = UDim2.new(0.65, -18, 1, 0)
TitleText.Position = UDim2.new(0, 18, 0, 0)
TitleText.BackgroundTransparency = 1
TitleText.Text = "Car Flipper - GomezXitado"
TitleText.TextColor3 = Theme.TextPrimary
TitleText.TextSize = 17
TitleText.Font = Enum.Font.GothamSemibold
TitleText.TextXAlignment = Enum.TextXAlignment.Left
TitleText.TextYAlignment = Enum.TextYAlignment.Center
TitleText.Parent = TitleBar

-- Data/Hora
local DateTimeText = Instance.new("TextLabel")
DateTimeText.Name = "DateTimeText"
DateTimeText.Size = UDim2.new(0.35, -10, 1, 0)
DateTimeText.Position = UDim2.new(0.65, 0, 0, 0)
DateTimeText.BackgroundTransparency = 1
DateTimeText.Text = "00:00 00/00/0000"
DateTimeText.TextColor3 = Theme.TextMuted
DateTimeText.TextSize = 14
DateTimeText.Font = Enum.Font.Gotham
DateTimeText.TextXAlignment = Enum.TextXAlignment.Right
DateTimeText.TextYAlignment = Enum.TextYAlignment.Center
DateTimeText.Parent = TitleBar

-- ═══════════════════════════════════════════════════════════════
-- 7. ABA AUTO
-- ═══════════════════════════════════════════════════════════════

local TabContainer = Instance.new("Frame")
TabContainer.Name = "TabContainer"
TabContainer.Size = UDim2.new(1, -20, 0, 42)
TabContainer.Position = UDim2.new(0, 10, 0, 54)
TabContainer.BackgroundTransparency = 1
TabContainer.Parent = MainFrame

local AutoTab = Instance.new("TextButton")
AutoTab.Name = "AutoTab"
AutoTab.Size = UDim2.new(0.15, 0, 1, -4)
AutoTab.Position = UDim2.new(0.425, 0, 0, 2)
AutoTab.BackgroundColor3 = Theme.Accent
AutoTab.Text = "Auto"
AutoTab.TextColor3 = Theme.Background
AutoTab.TextSize = 14
AutoTab.Font = Enum.Font.GothamMedium
AutoTab.BorderSizePixel = 0
AutoTab.Parent = TabContainer

local AutoTabCorner = Instance.new("UICorner")
AutoTabCorner.CornerRadius = UDim.new(0, 8)
AutoTabCorner.Parent = AutoTab

-- ═══════════════════════════════════════════════════════════════
-- 8. STATUS
-- ═══════════════════════════════════════════════════════════════

local StatusLabel = Instance.new("TextLabel")
StatusLabel.Name = "StatusLabel"
StatusLabel.Size = UDim2.new(1, -20, 0, 24)
StatusLabel.Position = UDim2.new(0, 12, 0, 104)
StatusLabel.BackgroundTransparency = 1
StatusLabel.Text = "[Car Flipper] Idle"
StatusLabel.TextColor3 = Theme.TextSecondary
StatusLabel.TextSize = 13
StatusLabel.Font = Enum.Font.GothamMedium
StatusLabel.TextXAlignment = Enum.TextXAlignment.Left
StatusLabel.TextYAlignment = Enum.TextYAlignment.Center
StatusLabel.Parent = MainFrame

-- ═══════════════════════════════════════════════════════════════
-- 9. CONTAINER DE CONTEÚDO
-- ═══════════════════════════════════════════════════════════════

local ContentContainer = Instance.new("Frame")
ContentContainer.Name = "ContentContainer"
ContentContainer.Size = UDim2.new(1, -20, 1, -150)
ContentContainer.Position = UDim2.new(0, 10, 0, 136)
ContentContainer.BackgroundTransparency = 1
ContentContainer.ClipsDescendants = true
ContentContainer.Parent = MainFrame

local Canvas = Instance.new("Frame")
Canvas.Name = "Canvas"
Canvas.Size = UDim2.new(1, 0, 0, 0)
Canvas.BackgroundTransparency = 1
Canvas.Parent = ContentContainer

-- ═══════════════════════════════════════════════════════════════
-- 10. SISTEMA DE CHECKBOX
-- ═══════════════════════════════════════════════════════════════

local Checkboxes = {}

function CreateCheckbox(parent, name, labelText, xPos, yPos)
    local container = Instance.new("Frame")
    container.Name = name
    container.Size = UDim2.new(0, 165, 0, 30)
    container.Position = UDim2.new(xPos, 0, 0, yPos)
    container.BackgroundTransparency = 1
    container.Parent = parent
    
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
    
    local label = Instance.new("TextLabel")
    label.Name = "Label"
    label.Size = UDim2.new(1, -26, 1, 0)
    label.Position = UDim2.new(0, 26, 0, 0)
    label.BackgroundTransparency = 1
    label.Text = labelText
    label.TextColor3 = Theme.TextPrimary
    label.TextSize = 13
    label.Font = Enum.Font.Gotham
    label.TextXAlignment = Enum.TextXAlignment.Left
    label.TextYAlignment = Enum.TextYAlignment.Center
    label.Parent = container
    
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
    
    container.InputBegan:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1 then
            state:Toggle()
        end
    end)
    
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
-- 11. CONSTRUÇÃO DAS SEÇÕES
-- ═══════════════════════════════════════════════════════════════

local SectionsData = {
    {
        title = "Base",
        items = {
            "Claim Cash",
            "Claim Parts",
            "Buy Upgrades",
            "Claim Containers",
            "Open Containers",
            "Claim Quests",
            "Deliver Junk",
        }
    },
    {
        title = "Cars",
        items = {
            "Fix Cars",
            "Sell Fixed Cars",
            "Update Stands",
            "Buy, Fix, Sell Cheapest",
        }
    },
    {
        title = "Rewards",
        items = {
            "Claim Playtime Rewards",
            "Claim Daily Rewards",
        }
    }
}

local sectionWidth = 280
local spacing = 20
local startX = 10

local totalWidth = (#SectionsData * (sectionWidth + spacing)) - spacing + 20

for i, sectionData in ipairs(SectionsData) do
    local xPos = startX + ((i - 1) * (sectionWidth + spacing))
    
    local sectionContainer = Instance.new("Frame")
    sectionContainer.Name = sectionData.title .. "Section"
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
    
    local divider = Instance.new("Frame")
    divider.Name = "Divider"
    divider.Size = UDim2.new(0.9, 0, 0, 1)
    divider.Position = UDim2.new(0.05, 0, 0, 40)
    divider.BackgroundColor3 = Theme.Divider
    divider.BorderSizePixel = 0
    divider.Parent = sectionContainer
    
    local startY = 48
    for j, labelText in ipairs(sectionData.items) do
        local yPos = startY + ((j - 1) * 32)
        local name = labelText:gsub(" ", ""):gsub(",", ""):gsub("%.", ""):gsub("-", "")
        CreateCheckbox(sectionContainer, name, labelText, 0, yPos)
    end
    
    local totalHeight = startY + (#sectionData.items * 32) + 16
    sectionContainer.Size = UDim2.new(0, sectionWidth, 0, totalHeight)
end

Canvas.Size = UDim2.new(0, totalWidth, 0, 380)

-- ═══════════════════════════════════════════════════════════════
-- 12. SISTEMA DE ARRASTAR
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
-- 13. FUNÇÕES DE ABRIR/FECHAR
-- ═══════════════════════════════════════════════════════════════

local isOpen = true -- Começa aberto
local toggleAnimating = false

function ToggleGUI()
    if toggleAnimating then return end
    toggleAnimating = true
    
    isOpen = not isOpen
    
    if isOpen then
        -- Abrir
        MainFrame.Visible = true
        MainFrame.Size = UDim2.new(0, 920, 0, 0)
        MainFrame.Position = UDim2.new(0.5, -460, 0.5, 0)
        
        TweenService:Create(MainFrame, TweenInfo.new(0.5, Enum.EasingStyle.Back, Enum.EasingDirection.Out), {
            Size = UDim2.new(0, 920, 0, 560),
            Position = UDim2.new(0.5, -460, 0.5, -280)
        }):Play()
        
        ClockSystem.isRunning = true
        CarFlipperAPI.RefreshClock()
        
        -- Mudar ícone do botão
        TweenService:Create(ToggleButton, TweenInfo.new(0.3), {
            ImageColor3 = Color3.fromRGB(200, 200, 210)
        }):Play()
        
    else
        -- Fechar
        TweenService:Create(MainFrame, TweenInfo.new(0.3, Enum.EasingStyle.Quint), {
            Size = UDim2.new(0, 920, 0, 0),
            Position = UDim2.new(0.5, -460, 0.5, 0)
        }):Play()
        
        ClockSystem.isRunning = false
        
        -- Mudar ícone do botão para indicar que está fechado
        TweenService:Create(ToggleButton, TweenInfo.new(0.3), {
            ImageColor3 = Color3.fromRGB(150, 150, 160)
        }):Play()
        
        task.wait(0.3)
        MainFrame.Visible = false
    end
    
    toggleAnimating = false
end

-- ═══════════════════════════════════════════════════════════════
-- 14. EVENTO DO BOTÃO FLUTUANTE
-- ═══════════════════════════════════════════════════════════════

ToggleButton.MouseButton1Click:Connect(ToggleGUI)

-- ═══════════════════════════════════════════════════════════════
-- 15. ATUALIZAÇÃO EM TEMPO REAL - RELÓGIO
-- ═══════════════════════════════════════════════════════════════

function UpdateClock()
    if not ClockSystem.isRunning then return end
    
    local newTime = ClockSystem:GetCurrentDateTime()
    if newTime ~= DateTimeText.Text then
        DateTimeText.Text = newTime
    end
end

RunService.Heartbeat:Connect(function()
    UpdateClock()
end)

spawn(function()
    while true do
        task.wait(1)
        UpdateClock()
    end
end)

-- ═══════════════════════════════════════════════════════════════
-- 16. API PÚBLICA
-- ═══════════════════════════════════════════════════════════════

local CarFlipperAPI = {
    GetStatus = function()
        return StatusLabel.Text
    end,
    SetStatus = function(text)
        StatusLabel.Text = text
    end,
    SetIdle = function()
        StatusLabel.Text = "[Car Flipper] Idle"
    end,
    
    GetTime = function()
        return DateTimeText.Text
    end,
    RefreshClock = function()
        ClockSystem.currentTime = ClockSystem:GetCurrentDateTime()
        DateTimeText.Text = ClockSystem.currentTime
        return DateTimeText.Text
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
    
    Toggle = ToggleGUI,
    Open = function()
        if not isOpen then ToggleGUI() end
    end,
    Close = function()
        if isOpen then ToggleGUI() end
    end,
    IsOpen = function()
        return isOpen
    end,
}

_G.CarFlipper = CarFlipperAPI

-- ═══════════════════════════════════════════════════════════════
-- 17. FINALIZAÇÃO
-- ═══════════════════════════════════════════════════════════════

print("✅ Car Flipper - GomezXitado carregado com sucesso!")
print("📌 Botão preto no canto superior direito (ao lado da engrenagem)")
print("🔄 Interface abre automaticamente ao executar")
print("📌 Clique no botão para abrir/fechar")
print("📌 Use _G.CarFlipper para controlar a interface")

CarFlipperAPI.SetStatus("[Car Flipper] Aguardando ações...")
CarFlipperAPI.RefreshClock()

-- A interface já está visível (isOpen = true)

-- Animação de entrada ao iniciar
task.wait(0.1)
MainFrame.Size = UDim2.new(0, 0, 0, 0)
MainFrame.Position = UDim2.new(0.5, 0, 0.5, 0)

task.wait(0.1)

TweenService:Create(MainFrame, TweenInfo.new(0.6, Enum.EasingStyle.Back, Enum.EasingDirection.Out), {
    Size = UDim2.new(0, 920, 0, 560),
    Position = UDim2.new(0.5, -460, 0.5, -280)
}):Play()

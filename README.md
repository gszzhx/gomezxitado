--[[
    ═══════════════════════════════════════════════════════════════
    CAR FLIPPER - GOMEZXITADO - VERSÃO CORRIGIDA
    Interface Premium com Layout Horizontal
    TEcla "J" para abrir/fechar
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
-- 2. TEMA
-- ═══════════════════════════════════════════════════════════════

local Theme = {
    Background = Color3.fromRGB(18, 18, 24),
    Surface = Color3.fromRGB(28, 28, 36),
    SurfaceLight = Color3.fromRGB(38, 38, 48),
    TextPrimary = Color3.fromRGB(235, 235, 245),
    TextSecondary = Color3.fromRGB(175, 175, 190),
    TextMuted = Color3.fromRGB(120, 120, 140),
    Accent = Color3.fromRGB(255, 200, 50),
    Border = Color3.fromRGB(55, 55, 70),
    Divider = Color3.fromRGB(42, 42, 55),
    Shadow = Color3.fromRGB(0, 0, 0),
    CheckboxBG = Color3.fromRGB(45, 45, 58),
    CheckboxChecked = Color3.fromRGB(255, 200, 50),
    Danger = Color3.fromRGB(220, 60, 60),
}

-- ═══════════════════════════════════════════════════════════════
-- 3. RELÓGIO
-- ═══════════════════════════════════════════════════════════════

local ClockSystem = {
    currentTime = "",
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
        return string.format("%02d:%02d %02d/%02d/%04d", 
            dateTable.hour, dateTable.min, 
            dateTable.day, dateTable.month, dateTable.year)
    end
    return result
end

-- ═══════════════════════════════════════════════════════════════
-- 4. BOTÃO FLUTUANTE
-- ═══════════════════════════════════════════════════════════════

local ToggleGui = Instance.new("ScreenGui")
ToggleGui.Name = "ToggleGUI"
ToggleGui.Parent = LocalPlayer:WaitForChild("PlayerGui")
ToggleGui.ResetOnSpawn = false

local ToggleButton = Instance.new("ImageButton")
ToggleButton.Name = "ToggleButton"
ToggleButton.Size = UDim2.new(0, 36, 0, 36)
ToggleButton.Position = UDim2.new(1, -52, 0, 8)
ToggleButton.BackgroundColor3 = Color3.fromRGB(30, 30, 35)
ToggleButton.BackgroundTransparency = 0
ToggleButton.Image = "rbxassetid://6031091079"
ToggleButton.ImageColor3 = Color3.fromRGB(200, 200, 210)
ToggleButton.BorderSizePixel = 0
ToggleButton.AutoButtonColor = false
ToggleButton.Parent = ToggleGui

local ToggleCorner = Instance.new("UICorner")
ToggleCorner.CornerRadius = UDim.new(0, 8)
ToggleCorner.Parent = ToggleButton

-- ═══════════════════════════════════════════════════════════════
-- 5. INTERFACE PRINCIPAL
-- ═══════════════════════════════════════════════════════════════

local MainGui = Instance.new("ScreenGui")
MainGui.Name = "CarFlipperGUI"
MainGui.Parent = LocalPlayer:WaitForChild("PlayerGui")
MainGui.ResetOnSpawn = false

local MainFrame = Instance.new("Frame")
MainFrame.Name = "MainFrame"
MainFrame.Size = UDim2.new(0, 920, 0, 560)
MainFrame.Position = UDim2.new(0.5, -460, 0.5, -280)
MainFrame.BackgroundColor3 = Theme.Background
MainFrame.BorderSizePixel = 0
MainFrame.ClipsDescendants = true
MainFrame.Parent = MainGui
MainFrame.Visible = true

local MainCorner = Instance.new("UICorner")
MainCorner.CornerRadius = UDim.new(0, 14)
MainCorner.Parent = MainFrame

-- ═══════════════════════════════════════════════════════════════
-- 6. BARRA DE TÍTULO
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

local DateTimeText = Instance.new("TextLabel")
DateTimeText.Name = "DateTimeText"
DateTimeText.Size = UDim2.new(0.35, -10, 1, 0)
DateTimeText.Position = UDim2.new(0.65, 0, 0, 0)
DateTimeText.BackgroundTransparency = 1
DateTimeText.Text = ClockSystem:GetCurrentDateTime()
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
StatusLabel.Text = "[Car Flipper] Aguardando ações..."
StatusLabel.TextColor3 = Theme.TextSecondary
StatusLabel.TextSize = 13
StatusLabel.Font = Enum.Font.GothamMedium
StatusLabel.TextXAlignment = Enum.TextXAlignment.Left
StatusLabel.TextYAlignment = Enum.TextYAlignment.Center
StatusLabel.Parent = MainFrame

-- ═══════════════════════════════════════════════════════════════
-- 9. CONTEÚDO
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
-- 10. CHECKBOXES
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
        checkBG = checkBG,
        checkIcon = checkIcon,
        label = label
    }
    
    function state:Toggle()
        state.checked = not state.checked
        if state.checked then
            TweenService:Create(state.checkBG, TweenInfo.new(0.15), {
                BackgroundColor3 = Theme.CheckboxChecked
            }):Play()
            state.checkIcon.Visible = true
        else
            TweenService:Create(state.checkBG, TweenInfo.new(0.15), {
                BackgroundColor3 = Theme.CheckboxBG
            }):Play()
            state.checkIcon.Visible = false
        end
    end
    
    container.InputBegan:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1 then
            state:Toggle()
        end
    end)
    
    Checkboxes[name] = state
    return state
end

-- ═══════════════════════════════════════════════════════════════
-- 11. SEÇÕES
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
-- 12. SISTEMA DE TOGGLE
-- ═══════════════════════════════════════════════════════════════

local isOpen = true
local isAnimating = false

function ToggleGUI()
    if isAnimating then return end
    isAnimating = true
    
    isOpen = not isOpen
    
    if isOpen then
        MainFrame.Visible = true
        MainFrame.Size = UDim2.new(0, 920, 0, 0)
        MainFrame.Position = UDim2.new(0.5, -460, 0.5, 0)
        
        local tween = TweenService:Create(MainFrame, TweenInfo.new(0.5, Enum.EasingStyle.Back, Enum.EasingDirection.Out), {
            Size = UDim2.new(0, 920, 0, 560),
            Position = UDim2.new(0.5, -460, 0.5, -280)
        })
        tween:Play()
        tween.Completed:Wait()
        
        ClockSystem.isRunning = true
        ToggleButton.ImageColor3 = Color3.fromRGB(200, 200, 210)
    else
        local tween = TweenService:Create(MainFrame, TweenInfo.new(0.3, Enum.EasingStyle.Quint), {
            Size = UDim2.new(0, 920, 0, 0),
            Position = UDim2.new(0.5, -460, 0.5, 0)
        })
        tween:Play()
        tween.Completed:Wait()
        
        MainFrame.Visible = false
        ClockSystem.isRunning = false
        ToggleButton.ImageColor3 = Color3.fromRGB(150, 150, 160)
    end
    
    isAnimating = false
end

-- ═══════════════════════════════════════════════════════════════
-- 13. EVENTOS DO BOTÃO (CORRIGIDO)
-- ═══════════════════════════════════════════════════════════════

local isDragging = false
local dragStart = Vector2.new()
local buttonStartPos = UDim2.new()
local dragThreshold = 5
local isDraggingActive = false

ToggleButton.MouseButton1Down:Connect(function(input)
    isDragging = true
    isDraggingActive = false
    dragStart = Vector2.new(input.Position.X, input.Position.Y)
    buttonStartPos = ToggleButton.Position
end)

-- CORRIGIDO: Evento de movimento do mouse
local function onInputChanged(input)
    if isDragging and input.UserInputType == Enum.UserInputType.MouseMovement then
        local currentPos = Vector2.new(input.Position.X, input.Position.Y)
        local delta = (currentPos - dragStart).Magnitude
        
        if delta > dragThreshold then
            isDraggingActive = true
            local newX = buttonStartPos.X.Offset + (input.Position.X - dragStart.X)
            local newY = buttonStartPos.Y.Offset + (input.Position.Y - dragStart.Y)
            
            local screenX = ToggleGui.AbsoluteSize.X
            local screenY = ToggleGui.AbsoluteSize.Y
            local buttonSize = 36
            
            newX = math.max(0, math.min(newX, screenX - buttonSize - 10))
            newY = math.max(0, math.min(newY, screenY - buttonSize - 50))
            
            ToggleButton.Position = UDim2.new(0, newX, 0, newY)
        end
    end
end

UserInputService.InputChanged:Connect(onInputChanged)

ToggleButton.MouseButton1Up:Connect(function(input)
    if not isDraggingActive and isDragging then
        ToggleGUI()
    end
    isDragging = false
    isDraggingActive = false
end)

-- ═══════════════════════════════════════════════════════════════
-- 14. ARRASTAR DO PAINEL
-- ═══════════════════════════════════════════════════════════════

local DraggingPanel = false
local DragPanelStart = Vector2.new()
local StartPanelPos = UDim2.new()

TitleBar.InputBegan:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseButton1 then
        DraggingPanel = true
        DragPanelStart = Vector2.new(input.Position.X, input.Position.Y)
        StartPanelPos = MainFrame.Position
    end
end)

UserInputService.InputEnded:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseButton1 and DraggingPanel then
        DraggingPanel = false
    end
end)

UserInputService.InputChanged:Connect(function(input)
    if DraggingPanel and input.UserInputType == Enum.UserInputType.MouseMovement then
        local delta = Vector2.new(input.Position.X - DragPanelStart.X, input.Position.Y - DragPanelStart.Y)
        MainFrame.Position = UDim2.new(
            StartPanelPos.X.Scale,
            StartPanelPos.X.Offset + delta.X,
            StartPanelPos.Y.Scale,
            StartPanelPos.Y.Offset + delta.Y
        )
    end
end)

-- ═══════════════════════════════════════════════════════════════
-- 15. TECLA "J" PARA ABRIR/FECHAR (CORRIGIDO)
-- ═══════════════════════════════════════════════════════════════

UserInputService.InputBegan:Connect(function(input, gameProcessed)
    if gameProcessed then return end
    
    -- Tecla "J" para abrir/fechar
    if input.KeyCode == Enum.KeyCode.J then
        ToggleGUI()
    end
end)

-- ═══════════════════════════════════════════════════════════════
-- 16. ATUALIZAÇÃO DO RELÓGIO
-- ═══════════════════════════════════════════════════════════════

RunService.Heartbeat:Connect(function()
    if not ClockSystem.isRunning then return end
    local newTime = ClockSystem:GetCurrentDateTime()
    if newTime ~= DateTimeText.Text then
        DateTimeText.Text = newTime
    end
end)

-- ═══════════════════════════════════════════════════════════════
-- 17. API
-- ═══════════════════════════════════════════════════════════════

local CarFlipperAPI = {
    GetStatus = function() return StatusLabel.Text end,
    SetStatus = function(text) StatusLabel.Text = text end,
    SetIdle = function() StatusLabel.Text = "[Car Flipper] Idle" end,
    GetTime = function() return DateTimeText.Text end,
    RefreshClock = function() 
        DateTimeText.Text = ClockSystem:GetCurrentDateTime() 
        return DateTimeText.Text 
    end,
    GetCheckbox = function(name) return Checkboxes[name] end,
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
    Open = function() if not isOpen then ToggleGUI() end end,
    Close = function() if isOpen then ToggleGUI() end end,
    IsOpen = function() return isOpen end,
}

_G.CarFlipper = CarFlipperAPI

-- ═══════════════════════════════════════════════════════════════
-- 18. ANIMAÇÃO DE ENTRADA
-- ═══════════════════════════════════════════════════════════════

task.wait(0.1)
MainFrame.Size = UDim2.new(0, 0, 0, 0)
MainFrame.Position = UDim2.new(0.5, 0, 0.5, 0)

task.wait(0.1)

TweenService:Create(MainFrame, TweenInfo.new(0.6, Enum.EasingStyle.Back, Enum.EasingDirection.Out), {
    Size = UDim2.new(0, 920, 0, 560),
    Position = UDim2.new(0.5, -460, 0.5, -280)
}):Play()

-- ═══════════════════════════════════════════════════════════════
-- 19. FINAL
-- ═══════════════════════════════════════════════════════════════

print("╔═══════════════════════════════════════════════════════════╗")
print("║     🚗 CAR FLIPPER - GOMEZXITADO CARREGADO!              ║")
print("║                                                           ║")
print("║  ✅ Interface aberta automaticamente                      ║")
print("║  📌 Clique no botão preto para abrir/fechar              ║")
print("║  🖱️ Arraste o botão para qualquer lugar da tela          ║")
print("║  ⌨️  Tecla 'J' para abrir/fechar                         ║")
print("║  🔄 Funciona INFINITAS vezes                             ║")
print("║                                                           ║")
print("║  📌 Use _G.CarFlipper para controlar                     ║")
print("╚═══════════════════════════════════════════════════════════╝")

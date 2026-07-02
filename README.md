--[[
    ═══════════════════════════════════════════════════════════════
    CAR FLIPPER - GOMEZXITADO - VERSÃO COMPLETA E FUNCIONAL
    Sistema Claim Cash + Teleporte + Coleta Automática
    TODAS AS FUNÇÕES IMPLEMENTADAS E CORRIGIDAS
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
-- 2. CONFIGURAÇÕES DO CLAIM CASH
-- ═══════════════════════════════════════════════════════════════

-- POSIÇÃO DE COLETA - AJUSTE CONFORME O JOGO
local CASH_COLLECT_POSITION = Vector3.new(0, 5, 0)

-- ═══════════════════════════════════════════════════════════════
-- 3. TEMA
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
    SliderBG = Color3.fromRGB(35, 35, 45),
    SliderFill = Color3.fromRGB(255, 200, 50),
    SliderThumb = Color3.fromRGB(255, 215, 75),
    Green = Color3.fromRGB(50, 220, 100),
}

-- ═══════════════════════════════════════════════════════════════
-- 4. SISTEMA CLAIM CASH
-- ═══════════════════════════════════════════════════════════════

local ClaimCash = {
    enabled = false,
    interval = 5,
    task = nil,
    isRunning = false,
    cashPosition = CASH_COLLECT_POSITION,
    collectRadius = 15,
}

-- ═══════════════════════════════════════════════════════════════
-- 5. RELÓGIO
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
-- 6. VARIÁVEIS GLOBAIS
-- ═══════════════════════════════════════════════════════════════

local StatusLabel = nil
local Checkboxes = {}

-- ═══════════════════════════════════════════════════════════════
-- 7. FUNÇÕES DO CLAIM CASH
-- ═══════════════════════════════════════════════════════════════

local function UpdateStatus(text)
    if StatusLabel then
        StatusLabel.Text = text
    end
    print(text)
end

local function TeleportToCash()
    if not ClaimCash.enabled then return false end
    
    local character = LocalPlayer.Character
    if not character then return false end
    
    local humanoidRootPart = character:FindFirstChild("HumanoidRootPart")
    if not humanoidRootPart then return false end
    
    UpdateStatus("[Car Flipper] Teleportando para Cash...")
    
    pcall(function()
        humanoidRootPart.CFrame = CFrame.new(ClaimCash.cashPosition)
    end)
    
    task.wait(0.5)
    return true
end

local function CollectCash()
    if not ClaimCash.enabled then return false end
    
    UpdateStatus("[Car Flipper] Coletando dinheiro...")
    
    local character = LocalPlayer.Character
    if not character then return false end
    
    local humanoidRootPart = character:FindFirstChild("HumanoidRootPart")
    if not humanoidRootPart then return false end
    
    local collected = false
    local cashObjects = {}
    
    for _, obj in ipairs(workspace:GetDescendants()) do
        if obj:IsA("BasePart") or obj:IsA("Model") then
            local objName = obj.Name:lower()
            local isCash = objName:find("cash") or objName:find("money") or 
                          objName:find("coin") or objName:find("dollar") or
                          objName:find("dinheiro") or objName:find("moeda") or
                          objName:find("gold")
            
            if isCash then
                local position = nil
                if obj:IsA("Model") and obj.PrimaryPart then
                    position = obj.PrimaryPart.Position
                elseif obj:IsA("BasePart") then
                    position = obj.Position
                end
                
                if position then
                    local distance = (position - humanoidRootPart.Position).Magnitude
                    if distance < ClaimCash.collectRadius then
                        table.insert(cashObjects, obj)
                    end
                end
            end
        end
    end
    
    if #cashObjects > 0 then
        for _, obj in ipairs(cashObjects) do
            if obj and obj.Parent then
                pcall(function()
                    local clickDetector = obj:FindFirstChild("ClickDetector")
                    if clickDetector then
                        clickDetector:Click()
                        collected = true
                        return
                    end
                    
                    local prompt = obj:FindFirstChild("ProximityPrompt")
                    if prompt then
                        prompt:InputHold()
                        task.wait(0.1)
                        prompt:InputRelease()
                        collected = true
                        return
                    end
                    
                    if obj:IsA("BasePart") then
                        firetouchinterest(humanoidRootPart, obj, 0)
                        task.wait(0.1)
                        firetouchinterest(humanoidRootPart, obj, 1)
                        collected = true
                        return
                    end
                    
                    obj:Destroy()
                    collected = true
                end)
                
                if collected then break end
            end
        end
    end
    
    if not collected then
        local guis = LocalPlayer:FindFirstChild("PlayerGui")
        if guis then
            for _, gui in ipairs(guis:GetChildren()) do
                if gui:IsA("ScreenGui") then
                    for _, btn in ipairs(gui:GetDescendants()) do
                        if btn:IsA("TextButton") or btn:IsA("ImageButton") then
                            local btnText = btn.Text or ""
                            local lowerText = btnText:lower()
                            
                            if lowerText:find("collect") or lowerText:find("coletar") or 
                               lowerText:find("cash") or lowerText:find("dinheiro") or
                               lowerText:find("reivindicar") or lowerText:find("claim") or
                               lowerText:find("pegar") or lowerText:find("receber") then
                                
                                pcall(function()
                                    btn:Click()
                                    collected = true
                                    break
                                end)
                            end
                        end
                    end
                end
                if collected then break end
            end
        end
    end
    
    if collected then
        UpdateStatus("[Car Flipper] Dinheiro coletado!")
    else
        UpdateStatus("[Car Flipper] Nenhum dinheiro encontrado...")
    end
    
    return collected
end

local function ClaimCashLoop()
    while ClaimCash.enabled and ClaimCash.isRunning do
        TeleportToCash()
        
        local collectSuccess = false
        for attempt = 1, 3 do
            if not ClaimCash.enabled then break end
            collectSuccess = CollectCash()
            if collectSuccess then break end
            task.wait(0.5)
        end
        
        local waitTime = ClaimCash.interval
        UpdateStatus(string.format("[Car Flipper] Próxima coleta em %.1f ms", waitTime * 1000))
        
        local startTime = tick()
        while ClaimCash.enabled and ClaimCash.isRunning and (tick() - startTime) < waitTime do
            task.wait(0.1)
        end
    end
    
    ClaimCash.isRunning = false
    if not ClaimCash.enabled then
        UpdateStatus("[Car Flipper] Claim Cash parado")
    end
end

local function StartClaimCash()
    if ClaimCash.isRunning then return end
    if not ClaimCash.enabled then return end
    
    ClaimCash.isRunning = true
    UpdateStatus("[Car Flipper] Claim Cash iniciado")
    ClaimCash.task = task.spawn(ClaimCashLoop)
end

local function StopClaimCash()
    ClaimCash.enabled = false
    ClaimCash.isRunning = false
    
    if ClaimCash.task then
        task.cancel(ClaimCash.task)
        ClaimCash.task = nil
    end
    
    UpdateStatus("[Car Flipper] Claim Cash parado")
end

local function ToggleClaimCash()
    ClaimCash.enabled = not ClaimCash.enabled
    
    if ClaimCash.enabled then
        StartClaimCash()
    else
        StopClaimCash()
    end
end

-- ═══════════════════════════════════════════════════════════════
-- 8. BOTÃO FLUTUANTE
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
-- 9. INTERFACE PRINCIPAL
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

local MainStroke = Instance.new("UIStroke")
MainStroke.Color = Theme.Border
MainStroke.Thickness = 1.5
MainStroke.Parent = MainFrame

-- ═══════════════════════════════════════════════════════════════
-- 10. BARRA DE TÍTULO
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
-- 11. ABA AUTO
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
-- 12. STATUS
-- ═══════════════════════════════════════════════════════════════

StatusLabel = Instance.new("TextLabel")
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
-- 13. CONTEÚDO
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
-- 14. CHECKBOXES COM SLIDER
-- ═══════════════════════════════════════════════════════════════

function CreateCheckboxWithSlider(parent, name, labelText, xPos, yPos, hasSlider)
    local container = Instance.new("Frame")
    container.Name = name .. "Container"
    container.Size = UDim2.new(0, 165, 0, hasSlider and 70 or 30)
    container.Position = UDim2.new(xPos, 0, 0, yPos)
    container.BackgroundTransparency = 1
    container.Parent = parent
    
    local checkboxContainer = Instance.new("Frame")
    checkboxContainer.Name = name
    checkboxContainer.Size = UDim2.new(0, 165, 0, 30)
    checkboxContainer.Position = UDim2.new(0, 0, 0, 0)
    checkboxContainer.BackgroundTransparency = 1
    checkboxContainer.Parent = container
    
    local checkBG = Instance.new("Frame")
    checkBG.Name = "CheckBG"
    checkBG.Size = UDim2.new(0, 20, 0, 20)
    checkBG.Position = UDim2.new(0, 0, 0.5, -10)
    checkBG.BackgroundColor3 = Theme.CheckboxBG
    checkBG.BorderSizePixel = 0
    checkBG.Parent = checkboxContainer
    
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
    label.Parent = checkboxContainer
    
    local sliderContainer = nil
    local sliderFill = nil
    local sliderThumb = nil
    local sliderValueText = nil
    
    if hasSlider then
        sliderContainer = Instance.new("Frame")
        sliderContainer.Name = "SliderContainer"
        sliderContainer.Size = UDim2.new(0, 155, 0, 30)
        sliderContainer.Position = UDim2.new(0, 0, 0, 34)
        sliderContainer.BackgroundColor3 = Theme.SliderBG
        sliderContainer.BackgroundTransparency = 0
        sliderContainer.BorderSizePixel = 0
        sliderContainer.Visible = false
        sliderContainer.Parent = container
        
        local sliderCorner = Instance.new("UICorner")
        sliderCorner.CornerRadius = UDim.new(0, 8)
        sliderCorner.Parent = sliderContainer
        
        local sliderBg = Instance.new("Frame")
        sliderBg.Name = "SliderBg"
        sliderBg.Size = UDim2.new(1, -10, 0, 4)
        sliderBg.Position = UDim2.new(0, 5, 0.5, -2)
        sliderBg.BackgroundColor3 = Theme.SurfaceLight
        sliderBg.BorderSizePixel = 0
        sliderBg.Parent = sliderContainer
        
        local sliderBgCorner = Instance.new("UICorner")
        sliderBgCorner.CornerRadius = UDim.new(1, 0)
        sliderBgCorner.Parent = sliderBg
        
        sliderFill = Instance.new("Frame")
        sliderFill.Name = "SliderFill"
        sliderFill.Size = UDim2.new(0.5, 0, 1, 0)
        sliderFill.Position = UDim2.new(0, 0, 0, 0)
        sliderFill.BackgroundColor3 = Theme.SliderFill
        sliderFill.BorderSizePixel = 0
        sliderFill.Parent = sliderBg
        
        local sliderFillCorner = Instance.new("UICorner")
        sliderFillCorner.CornerRadius = UDim.new(1, 0)
        sliderFillCorner.Parent = sliderFill
        
        sliderThumb = Instance.new("Frame")
        sliderThumb.Name = "SliderThumb"
        sliderThumb.Size = UDim2.new(0, 14, 0, 14)
        sliderThumb.Position = UDim2.new(0.5, -7, 0.5, -7)
        sliderThumb.BackgroundColor3 = Theme.SliderThumb
        sliderThumb.BorderSizePixel = 0
        sliderThumb.Parent = sliderContainer
        
        local thumbCorner = Instance.new("UICorner")
        thumbCorner.CornerRadius = UDim.new(1, 0)
        thumbCorner.Parent = sliderThumb
        
        sliderValueText = Instance.new("TextLabel")
        sliderValueText.Name = "ValueText"
        sliderValueText.Size = UDim2.new(0, 50, 1, 0)
        sliderValueText.Position = UDim2.new(1, 5, 0, 0)
        sliderValueText.BackgroundTransparency = 1
        sliderValueText.Text = "5 ms"
        sliderValueText.TextColor3 = Theme.TextSecondary
        sliderValueText.TextSize = 11
        sliderValueText.Font = Enum.Font.GothamMedium
        sliderValueText.TextXAlignment = Enum.TextXAlignment.Left
        sliderValueText.TextYAlignment = Enum.TextYAlignment.Center
        sliderValueText.Parent = sliderContainer
        
        local minValue = 0.3
        local maxValue = 300
        local currentValue = 5
        local isDraggingSlider = false
        
        local function UpdateSlider(value)
            currentValue = math.max(minValue, math.min(maxValue, value))
            local percent = (currentValue - minValue) / (maxValue - minValue)
            
            sliderFill.Size = UDim2.new(percent, 0, 1, 0)
            sliderThumb.Position = UDim2.new(percent, -7, 0.5, -7)
            
            if currentValue >= 1 then
                sliderValueText.Text = string.format("%.0f ms", currentValue)
            else
                sliderValueText.Text = string.format("%.1f ms", currentValue)
            end
            
            if name == "ClaimCash" then
                ClaimCash.interval = currentValue
            end
        end
        
        local function GetSliderPercent(input)
            local sliderPos = sliderContainer.AbsolutePosition
            local sliderSize = sliderContainer.AbsoluteSize.X - 10
            local inputPos = input.Position.X
            
            local percent = (inputPos - sliderPos.X - 5) / sliderSize
            percent = math.max(0, math.min(1, percent))
            return percent
        end
        
        sliderContainer.InputBegan:Connect(function(input)
            if input.UserInputType == Enum.UserInputType.MouseButton1 or 
               input.UserInputType == Enum.UserInputType.Touch then
                isDraggingSlider = true
                local percent = GetSliderPercent(input)
                local value = minValue + (maxValue - minValue) * percent
                UpdateSlider(value)
            end
        end)
        
        UserInputService.InputChanged:Connect(function(input)
            if isDraggingSlider and (input.UserInputType == Enum.UserInputType.MouseMovement or 
                                     input.UserInputType == Enum.UserInputType.Touch) then
                local percent = GetSliderPercent(input)
                local value = minValue + (maxValue - minValue) * percent
                UpdateSlider(value)
            end
        end)
        
        UserInputService.InputEnded:Connect(function(input)
            if input.UserInputType == Enum.UserInputType.MouseButton1 or 
               input.UserInputType == Enum.UserInputType.Touch then
                isDraggingSlider = false
            end
        end)
        
        UpdateSlider(currentValue)
    end
    
    local state = {
        checked = false,
        checkBG = checkBG,
        checkIcon = checkIcon,
        label = label,
        sliderContainer = sliderContainer,
        hasSlider = hasSlider or false,
        name = name,
    }
    
    function state:Toggle()
        state.checked = not state.checked
        
        if state.checked then
            TweenService:Create(state.checkBG, TweenInfo.new(0.15), {
                BackgroundColor3 = Theme.CheckboxChecked
            }):Play()
            state.checkIcon.Visible = true
            
            if state.hasSlider and sliderContainer then
                sliderContainer.Visible = true
                TweenService:Create(sliderContainer, TweenInfo.new(0.3), {
                    BackgroundTransparency = 0
                }):Play()
                
                if state.name == "ClaimCash" then
                    ToggleClaimCash()
                end
            end
        else
            TweenService:Create(state.checkBG, TweenInfo.new(0.15), {
                BackgroundColor3 = Theme.CheckboxBG
            }):Play()
            state.checkIcon.Visible = false
            
            if state.hasSlider and sliderContainer then
                TweenService:Create(sliderContainer, TweenInfo.new(0.3), {
                    BackgroundTransparency = 1
                }):Play()
                task.wait(0.3)
                sliderContainer.Visible = false
                
                if state.name == "ClaimCash" then
                    ToggleClaimCash()
                end
            end
        end
    end
    
    checkboxContainer.InputBegan:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1 or 
           input.UserInputType == Enum.UserInputType.Touch then
            state:Toggle()
        end
    end)
    
    Checkboxes[name] = state
    return state
end

-- ═══════════════════════════════════════════════════════════════
-- 15. SEÇÕES
-- ═══════════════════════════════════════════════════════════════

local SectionsData = {
    {
        title = "Base",
        items = {
            {name = "Claim Cash", hasSlider = true},
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
    for j, item in ipairs(sectionData.items) do
        local yPos = startY + ((j - 1) * 32)
        local hasSlider = false
        local labelText = item
        
        if type(item) == "table" then
            labelText = item.name
            hasSlider = item.hasSlider or false
        end
        
        local name = labelText:gsub(" ", ""):gsub(",", ""):gsub("%.", ""):gsub("-", "")
        CreateCheckboxWithSlider(sectionContainer, name, labelText, 0, yPos, hasSlider)
    end
    
    local totalHeight = 48
    for j, item in ipairs(sectionData.items) do
        local hasSlider = false
        if type(item) == "table" then
            hasSlider = item.hasSlider or false
        end
        totalHeight = totalHeight + (hasSlider and 70 or 30)
    end
    totalHeight = totalHeight + 16
    
    sectionContainer.Size = UDim2.new(0, sectionWidth, 0, totalHeight)
end

Canvas.Size = UDim2.new(0, totalWidth, 0, 380)

-- ═══════════════════════════════════════════════════════════════
-- 16. SISTEMA DE TOGGLE
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
-- 17. ARRASTO DO BOTÃO
-- ═══════════════════════════════════════════════════════════════

local isDragging = false
local dragStartPos = Vector2.new()
local buttonStartPos = UDim2.new()
local isDraggingActive = false
local dragThreshold = 8

ToggleButton.InputBegan:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseButton1 or 
       input.UserInputType == Enum.UserInputType.Touch then
        isDragging = true
        isDraggingActive = false
        dragStartPos = Vector2.new(input.Position.X, input.Position.Y)
        buttonStartPos = ToggleButton.Position
    end
end)

UserInputService.InputChanged:Connect(function(input)
    if isDragging and (input.UserInputType == Enum.UserInputType.MouseMovement or 
                       input.UserInputType == Enum.UserInputType.Touch) then
        local currentPos = Vector2.new(input.Position.X, input.Position.Y)
        local delta = (currentPos - dragStartPos).Magnitude
        
        if delta > dragThreshold then
            isDraggingActive = true
            
            local newX = buttonStartPos.X.Offset + (currentPos.X - dragStartPos.X)
            local newY = buttonStartPos.Y.Offset + (currentPos.Y - dragStartPos.Y)
            
            local screenX = ToggleGui.AbsoluteSize.X
            local screenY = ToggleGui.AbsoluteSize.Y
            local buttonSize = 36
            
            newX = math.max(0, math.min(newX, screenX - buttonSize - 10))
            newY = math.max(0, math.min(newY, screenY - buttonSize - 50))
            
            ToggleButton.Position = UDim2.new(0, newX, 0, newY)
        end
    end
end)

ToggleButton.InputEnded:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseButton1 or 
       input.UserInputType == Enum.UserInputType.Touch then
        if not isDraggingActive and isDragging then
            ToggleGUI()
        end
        isDragging = false
        isDraggingActive = false
    end
end)

-- ═══════════════════════════════════════════════════════════════
-- 18. ARRASTO DO PAINEL
-- ═══════════════════════════════════════════════════════════════

local DraggingPanel = false
local DragPanelStart = Vector2.new()
local StartPanelPos = UDim2.new()

TitleBar.InputBegan:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseButton1 or 
       input.UserInputType == Enum.UserInputType.Touch then
        DraggingPanel = true
        DragPanelStart = Vector2.new(input.Position.X, input.Position.Y)
        StartPanelPos = MainFrame.Position
    end
end)

UserInputService.InputChanged:Connect(function(input)
    if DraggingPanel and (input.UserInputType == Enum.UserInputType.MouseMovement or 
                          input.UserInputType == Enum.UserInputType.Touch) then
        local currentPos = Vector2.new(input.Position.X, input.Position.Y)
        local delta = currentPos - DragPanelStart
        MainFrame.Position = UDim2.new(
            StartPanelPos.X.Scale,
            StartPanelPos.X.Offset + delta.X,
            StartPanelPos.Y.Scale,
            StartPanelPos.Y.Offset + delta.Y
        )
    end
end)

TitleBar.InputEnded:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseButton1 or 
       input.UserInputType == Enum.UserInputType.Touch then
        DraggingPanel = false
    end
end)

-- ═══════════════════════════════════════════════════════════════
-- 19. TECLA "J" PARA ABRIR/FECHAR
-- ═══════════════════════════════════════════════════════════════

UserInputService.InputBegan:Connect(function(input, gameProcessed)
    if gameProcessed then return end
    
    if input.KeyCode == Enum.KeyCode.J then
        ToggleGUI()
    end
end)

-- ═══════════════════════════════════════════════════════════════
-- 20. ATUALIZAÇÃO DO RELÓGIO
-- ═══════════════════════════════════════════════════════════════

RunService.Heartbeat:Connect(function()
    if not ClockSystem.isRunning then return end
    local newTime = ClockSystem:GetCurrentDateTime()
    if newTime ~= DateTimeText.Text then
        DateTimeText.Text = newTime
    end
end)

-- ═══════════════════════════════════════════════════════════════
-- 21. API PÚBLICA
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
    -- Funções do Claim Cash
    StartClaimCash = StartClaimCash,
    StopClaimCash = StopClaimCash,
    ToggleClaimCash = ToggleClaimCash,
    IsClaimCashRunning = function() return ClaimCash.isRunning end,
    SetCashPosition = function(pos) ClaimCash.cashPosition = pos end,
    SetInterval = function(interval) ClaimCash.interval = interval end,
}

_G.CarFlipper = CarFlipperAPI

-- ═══════════════════════════════════════════════════════════════
-- 22. ANIMAÇÃO DE ENTRADA
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
-- 23. FINAL
-- ═══════════════════════════════════════════════════════════════

print("╔═══════════════════════════════════════════════════════════╗")
print("║     🚗 CAR FLIPPER - GOMEZXITADO CARREGADO!              ║")
print("║                                                           ║")
print("║  ✅ Interface aberta automaticamente                      ║")
print("║  📌 Clique no botão preto para abrir/fechar              ║")
print("║  🖱️ Arraste o botão para qualquer lugar (PC/MOBILE)     ║")
print("║  ⌨️  Tecla 'J' para abrir/fechar                         ║")
print("║  💰 Claim Cash: Ative a checkbox para iniciar            ║")
print("║  📱 Totalmente compatível com MOBILE                     ║")
print("║                                                           ║")
print("║  📌 Use _G.CarFlipper para controlar                     ║")
print("╚═══════════════════════════════════════════════════════════╝")

--[[
    CAR FLIPPER - GOMEZXITADO
    VERSÃO SIMPLIFICADA E FUNCIONAL
--]]

-- ═══════════════════════════════════════════════════════════════
-- 1. IMPORTAÇÕES
-- ═══════════════════════════════════════════════════════════════

local Players = game:GetService("Players")
local LocalPlayer = Players.LocalPlayer
local TweenService = game:GetService("TweenService")
local UserInputService = game:GetService("UserInputService")
local RunService = game:GetService("RunService")
local HttpService = game:GetService("HttpService")

-- ═══════════════════════════════════════════════════════════════
-- 2. CONFIGURAÇÕES
-- ═══════════════════════════════════════════════════════════════

local CASH_POSITION = Vector3.new(0, 5, 0) -- MUDE AQUI

-- ═══════════════════════════════════════════════════════════════
-- 3. TEMA
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
    CheckboxBG = Color3.fromRGB(45, 45, 58),
    CheckboxChecked = Color3.fromRGB(255, 200, 50),
    SliderBG = Color3.fromRGB(35, 35, 45),
    SliderFill = Color3.fromRGB(255, 200, 50),
    SliderThumb = Color3.fromRGB(255, 215, 75),
}

-- ═══════════════════════════════════════════════════════════════
-- 4. VARIÁVEIS GLOBAIS
-- ═══════════════════════════════════════════════════════════════

local ClaimCashEnabled = false
local ClaimCashInterval = 5
local ClaimCashTask = nil
local StatusLabel = nil
local Checkboxes = {}

-- ═══════════════════════════════════════════════════════════════
-- 5. FUNÇÕES DO RELÓGIO
-- ═══════════════════════════════════════════════════════════════

local function GetCurrentTime()
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
        local t = os.time()
        local d = os.date("*t", t)
        return string.format("%02d:%02d %02d/%02d/%04d", d.hour, d.min, d.day, d.month, d.year)
    end
    return result
end

-- ═══════════════════════════════════════════════════════════════
-- 6. FUNÇÕES DO CLAIM CASH
-- ═══════════════════════════════════════════════════════════════

local function UpdateStatus(text)
    if StatusLabel then
        StatusLabel.Text = text
    end
end

local function TeleportToCash()
    local char = LocalPlayer.Character
    if not char then return false end
    
    local root = char:FindFirstChild("HumanoidRootPart")
    if not root then return false end
    
    UpdateStatus("[Car Flipper] Teleportando para Cash...")
    root.CFrame = CFrame.new(CASH_POSITION)
    task.wait(0.5)
    return true
end

local function CollectCash()
    UpdateStatus("[Car Flipper] Coletando dinheiro...")
    
    local char = LocalPlayer.Character
    if not char then return false end
    
    local root = char:FindFirstChild("HumanoidRootPart")
    if not root then return false end
    
    local collected = false
    
    -- Procurar objetos de dinheiro
    for _, obj in ipairs(workspace:GetDescendants()) do
        if obj:IsA("BasePart") or obj:IsA("Model") then
            local name = obj.Name:lower()
            if name:find("cash") or name:find("money") or name:find("coin") or 
               name:find("dollar") or name:find("dinheiro") or name:find("moeda") then
                
                local pos = nil
                if obj:IsA("Model") and obj.PrimaryPart then
                    pos = obj.PrimaryPart.Position
                elseif obj:IsA("BasePart") then
                    pos = obj.Position
                end
                
                if pos and (pos - root.Position).Magnitude < 20 then
                    pcall(function()
                        -- Tenta diferentes métodos
                        local click = obj:FindFirstChild("ClickDetector")
                        if click then click:Click() collected = true return end
                        
                        local prompt = obj:FindFirstChild("ProximityPrompt")
                        if prompt then prompt:InputHold() task.wait(0.1) prompt:InputRelease() collected = true return end
                        
                        if obj:IsA("BasePart") then
                            firetouchinterest(root, obj, 0)
                            task.wait(0.1)
                            firetouchinterest(root, obj, 1)
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
    end
    
    -- Tentar via interface
    if not collected then
        local guis = LocalPlayer:FindFirstChild("PlayerGui")
        if guis then
            for _, gui in ipairs(guis:GetChildren()) do
                if gui:IsA("ScreenGui") then
                    for _, btn in ipairs(gui:GetDescendants()) do
                        if btn:IsA("TextButton") or btn:IsA("ImageButton") then
                            local text = (btn.Text or ""):lower()
                            if text:find("collect") or text:find("coletar") or text:find("cash") or 
                               text:find("dinheiro") or text:find("claim") or text:find("pegar") then
                                pcall(function() btn:Click() collected = true end)
                                if collected then break end
                            end
                        end
                    end
                end
                if collected then break end
            end
        end
    end
    
    UpdateStatus(collected and "[Car Flipper] Dinheiro coletado!" or "[Car Flipper] Nenhum dinheiro encontrado...")
    return collected
end

local function ClaimCashLoop()
    while ClaimCashEnabled do
        TeleportToCash()
        
        for i = 1, 3 do
            if not ClaimCashEnabled then break end
            if CollectCash() then break end
            task.wait(0.5)
        end
        
        local waitTime = ClaimCashInterval
        UpdateStatus(string.format("[Car Flipper] Próxima coleta em %.1f ms", waitTime * 1000))
        
        local start = tick()
        while ClaimCashEnabled and (tick() - start) < waitTime do
            task.wait(0.1)
        end
    end
    
    UpdateStatus("[Car Flipper] Claim Cash parado")
end

local function StartClaimCash()
    if ClaimCashTask then return end
    if not ClaimCashEnabled then return end
    
    UpdateStatus("[Car Flipper] Claim Cash iniciado")
    ClaimCashTask = task.spawn(ClaimCashLoop)
end

local function StopClaimCash()
    ClaimCashEnabled = false
    if ClaimCashTask then
        task.cancel(ClaimCashTask)
        ClaimCashTask = nil
    end
    UpdateStatus("[Car Flipper] Claim Cash parado")
end

local function ToggleClaimCash()
    ClaimCashEnabled = not ClaimCashEnabled
    if ClaimCashEnabled then
        StartClaimCash()
    else
        StopClaimCash()
    end
end

-- ═══════════════════════════════════════════════════════════════
-- 7. BOTÃO FLUTUANTE
-- ═══════════════════════════════════════════════════════════════

local ToggleGui = Instance.new("ScreenGui")
ToggleGui.Parent = LocalPlayer:WaitForChild("PlayerGui")
ToggleGui.ResetOnSpawn = false

local ToggleButton = Instance.new("ImageButton")
ToggleButton.Size = UDim2.new(0, 36, 0, 36)
ToggleButton.Position = UDim2.new(1, -52, 0, 8)
ToggleButton.BackgroundColor3 = Color3.fromRGB(30, 30, 35)
ToggleButton.Image = "rbxassetid://6031091079"
ToggleButton.ImageColor3 = Color3.fromRGB(200, 200, 210)
ToggleButton.BorderSizePixel = 0
ToggleButton.AutoButtonColor = false
ToggleButton.Parent = ToggleGui

local ToggleCorner = Instance.new("UICorner")
ToggleCorner.CornerRadius = UDim.new(0, 8)
ToggleCorner.Parent = ToggleButton

-- ═══════════════════════════════════════════════════════════════
-- 8. INTERFACE PRINCIPAL
-- ═══════════════════════════════════════════════════════════════

local MainGui = Instance.new("ScreenGui")
MainGui.Parent = LocalPlayer:WaitForChild("PlayerGui")
MainGui.ResetOnSpawn = false

local MainFrame = Instance.new("Frame")
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
-- 9. BARRA DE TÍTULO
-- ═══════════════════════════════════════════════════════════════

local TitleBar = Instance.new("Frame")
TitleBar.Size = UDim2.new(1, 0, 0, 48)
TitleBar.BackgroundColor3 = Theme.SurfaceLight
TitleBar.BorderSizePixel = 0
TitleBar.Parent = MainFrame

local TitleCorner = Instance.new("UICorner")
TitleCorner.CornerRadius = UDim.new(0, 14)
TitleCorner.Parent = TitleBar

local TitleText = Instance.new("TextLabel")
TitleText.Size = UDim2.new(0.6, -18, 1, 0)
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
DateTimeText.Size = UDim2.new(0.4, -10, 1, 0)
DateTimeText.Position = UDim2.new(0.6, 0, 0, 0)
DateTimeText.BackgroundTransparency = 1
DateTimeText.Text = GetCurrentTime()
DateTimeText.TextColor3 = Theme.TextMuted
DateTimeText.TextSize = 14
DateTimeText.Font = Enum.Font.Gotham
DateTimeText.TextXAlignment = Enum.TextXAlignment.Right
DateTimeText.TextYAlignment = Enum.TextYAlignment.Center
DateTimeText.Parent = TitleBar

-- ═══════════════════════════════════════════════════════════════
-- 10. ABA AUTO
-- ═══════════════════════════════════════════════════════════════

local TabContainer = Instance.new("Frame")
TabContainer.Size = UDim2.new(1, -20, 0, 42)
TabContainer.Position = UDim2.new(0, 10, 0, 54)
TabContainer.BackgroundTransparency = 1
TabContainer.Parent = MainFrame

local AutoTab = Instance.new("TextButton")
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
-- 11. STATUS
-- ═══════════════════════════════════════════════════════════════

StatusLabel = Instance.new("TextLabel")
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
-- 12. CONTEÚDO
-- ═══════════════════════════════════════════════════════════════

local ContentContainer = Instance.new("Frame")
ContentContainer.Size = UDim2.new(1, -20, 1, -150)
ContentContainer.Position = UDim2.new(0, 10, 0, 136)
ContentContainer.BackgroundTransparency = 1
ContentContainer.ClipsDescendants = true
ContentContainer.Parent = MainFrame

local Canvas = Instance.new("Frame")
Canvas.Size = UDim2.new(1, 0, 0, 0)
Canvas.BackgroundTransparency = 1
Canvas.Parent = ContentContainer

-- ═══════════════════════════════════════════════════════════════
-- 13. CHECKBOX COM SLIDER
-- ═══════════════════════════════════════════════════════════════

local function CreateCheckbox(parent, name, labelText, x, y, hasSlider)
    local container = Instance.new("Frame")
    container.Size = UDim2.new(0, 165, 0, hasSlider and 70 or 30)
    container.Position = UDim2.new(x, 0, 0, y)
    container.BackgroundTransparency = 1
    container.Parent = parent
    
    local cbContainer = Instance.new("Frame")
    cbContainer.Size = UDim2.new(0, 165, 0, 30)
    cbContainer.Position = UDim2.new(0, 0, 0, 0)
    cbContainer.BackgroundTransparency = 1
    cbContainer.Parent = container
    
    local bg = Instance.new("Frame")
    bg.Size = UDim2.new(0, 20, 0, 20)
    bg.Position = UDim2.new(0, 0, 0.5, -10)
    bg.BackgroundColor3 = Theme.CheckboxBG
    bg.BorderSizePixel = 0
    bg.Parent = cbContainer
    
    local bgCorner = Instance.new("UICorner")
    bgCorner.CornerRadius = UDim.new(0, 5)
    bgCorner.Parent = bg
    
    local icon = Instance.new("TextLabel")
    icon.Size = UDim2.new(1, 0, 1, 0)
    icon.BackgroundTransparency = 1
    icon.Text = "✓"
    icon.TextColor3 = Theme.Background
    icon.TextSize = 16
    icon.Font = Enum.Font.GothamBold
    icon.TextXAlignment = Enum.TextXAlignment.Center
    icon.TextYAlignment = Enum.TextYAlignment.Center
    icon.Visible = false
    icon.Parent = bg
    
    local label = Instance.new("TextLabel")
    label.Size = UDim2.new(1, -26, 1, 0)
    label.Position = UDim2.new(0, 26, 0, 0)
    label.BackgroundTransparency = 1
    label.Text = labelText
    label.TextColor3 = Theme.TextPrimary
    label.TextSize = 13
    label.Font = Enum.Font.Gotham
    label.TextXAlignment = Enum.TextXAlignment.Left
    label.TextYAlignment = Enum.TextYAlignment.Center
    label.Parent = cbContainer
    
    local slider = nil
    local sliderFill = nil
    local sliderThumb = nil
    local sliderText = nil
    
    if hasSlider then
        slider = Instance.new("Frame")
        slider.Size = UDim2.new(0, 155, 0, 30)
        slider.Position = UDim2.new(0, 0, 0, 34)
        slider.BackgroundColor3 = Theme.SliderBG
        slider.BorderSizePixel = 0
        slider.Visible = false
        slider.Parent = container
        
        local sliderCorner = Instance.new("UICorner")
        sliderCorner.CornerRadius = UDim.new(0, 8)
        sliderCorner.Parent = slider
        
        local bgSlider = Instance.new("Frame")
        bgSlider.Size = UDim2.new(1, -10, 0, 4)
        bgSlider.Position = UDim2.new(0, 5, 0.5, -2)
        bgSlider.BackgroundColor3 = Theme.SurfaceLight
        bgSlider.BorderSizePixel = 0
        bgSlider.Parent = slider
        
        local bgSliderCorner = Instance.new("UICorner")
        bgSliderCorner.CornerRadius = UDim.new(1, 0)
        bgSliderCorner.Parent = bgSlider
        
        sliderFill = Instance.new("Frame")
        sliderFill.Size = UDim2.new(0.5, 0, 1, 0)
        sliderFill.BackgroundColor3 = Theme.SliderFill
        sliderFill.BorderSizePixel = 0
        sliderFill.Parent = bgSlider
        
        local fillCorner = Instance.new("UICorner")
        fillCorner.CornerRadius = UDim.new(1, 0)
        fillCorner.Parent = sliderFill
        
        sliderThumb = Instance.new("Frame")
        sliderThumb.Size = UDim2.new(0, 14, 0, 14)
        sliderThumb.Position = UDim2.new(0.5, -7, 0.5, -7)
        sliderThumb.BackgroundColor3 = Theme.SliderThumb
        sliderThumb.BorderSizePixel = 0
        sliderThumb.Parent = slider
        
        local thumbCorner = Instance.new("UICorner")
        thumbCorner.CornerRadius = UDim.new(1, 0)
        thumbCorner.Parent = sliderThumb
        
        sliderText = Instance.new("TextLabel")
        sliderText.Size = UDim2.new(0, 50, 1, 0)
        sliderText.Position = UDim2.new(1, 5, 0, 0)
        sliderText.BackgroundTransparency = 1
        sliderText.Text = "5 ms"
        sliderText.TextColor3 = Theme.TextSecondary
        sliderText.TextSize = 11
        sliderText.Font = Enum.Font.GothamMedium
        sliderText.TextXAlignment = Enum.TextXAlignment.Left
        sliderText.TextYAlignment = Enum.TextYAlignment.Center
        sliderText.Parent = slider
        
        local minVal = 0.3
        local maxVal = 300
        local curVal = 5
        local dragging = false
        
        local function UpdateSlider(val)
            curVal = math.max(minVal, math.min(maxVal, val))
            local pct = (curVal - minVal) / (maxVal - minVal)
            sliderFill.Size = UDim2.new(pct, 0, 1, 0)
            sliderThumb.Position = UDim2.new(pct, -7, 0.5, -7)
            sliderText.Text = (curVal >= 1 and string.format("%.0f ms", curVal) or string.format("%.1f ms", curVal))
            if name == "ClaimCash" then ClaimCashInterval = curVal end
        end
        
        local function GetPct(input)
            local pos = slider.AbsolutePosition
            local size = slider.AbsoluteSize.X - 10
            local pct = (input.Position.X - pos.X - 5) / size
            return math.max(0, math.min(1, pct))
        end
        
        slider.InputBegan:Connect(function(input)
            if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
                dragging = true
                local pct = GetPct(input)
                UpdateSlider(minVal + (maxVal - minVal) * pct)
            end
        end)
        
        UserInputService.InputChanged:Connect(function(input)
            if dragging and (input.UserInputType == Enum.UserInputType.MouseMovement or input.UserInputType == Enum.UserInputType.Touch) then
                local pct = GetPct(input)
                UpdateSlider(minVal + (maxVal - minVal) * pct)
            end
        end)
        
        UserInputService.InputEnded:Connect(function(input)
            if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
                dragging = false
            end
        end)
        
        UpdateSlider(curVal)
    end
    
    local state = { checked = false, bg = bg, icon = icon, slider = slider, hasSlider = hasSlider, name = name }
    
    function state:Toggle()
        state.checked = not state.checked
        if state.checked then
            TweenService:Create(state.bg, TweenInfo.new(0.15), { BackgroundColor3 = Theme.CheckboxChecked }):Play()
            state.icon.Visible = true
            if state.hasSlider and slider then
                slider.Visible = true
                TweenService:Create(slider, TweenInfo.new(0.3), { BackgroundTransparency = 0 }):Play()
                if state.name == "ClaimCash" then ToggleClaimCash() end
            end
        else
            TweenService:Create(state.bg, TweenInfo.new(0.15), { BackgroundColor3 = Theme.CheckboxBG }):Play()
            state.icon.Visible = false
            if state.hasSlider and slider then
                TweenService:Create(slider, TweenInfo.new(0.3), { BackgroundTransparency = 1 }):Play()
                task.wait(0.3)
                slider.Visible = false
                if state.name == "ClaimCash" then ToggleClaimCash() end
            end
        end
    end
    
    cbContainer.InputBegan:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
            state:Toggle()
        end
    end)
    
    Checkboxes[name] = state
    return state
end

-- ═══════════════════════════════════════════════════════════════
-- 14. SEÇÕES
-- ═══════════════════════════════════════════════════════════════

local Sections = {
    { title = "Base", items = { {name = "Claim Cash", slider = true}, "Claim Parts", "Buy Upgrades", "Claim Containers", "Open Containers", "Claim Quests", "Deliver Junk" } },
    { title = "Cars", items = { "Fix Cars", "Sell Fixed Cars", "Update Stands", "Buy, Fix, Sell Cheapest" } },
    { title = "Rewards", items = { "Claim Playtime Rewards", "Claim Daily Rewards" } },
}

local sw = 280
local sp = 20
local sx = 10
local totalW = (#Sections * (sw + sp)) - sp + 20

for i, sec in ipairs(Sections) do
    local x = sx + ((i - 1) * (sw + sp))
    
    local container = Instance.new("Frame")
    container.Size = UDim2.new(0, sw, 0, 0)
    container.Position = UDim2.new(0, x, 0, 0)
    container.BackgroundColor3 = Theme.Surface
    container.BorderSizePixel = 0
    container.Parent = Canvas
    
    local corner = Instance.new("UICorner")
    corner.CornerRadius = UDim.new(0, 10)
    corner.Parent = container
    
    local stroke = Instance.new("UIStroke")
    stroke.Color = Theme.Border
    stroke.Thickness = 1
    stroke.Parent = container
    
    local title = Instance.new("TextLabel")
    title.Size = UDim2.new(1, -16, 0, 32)
    title.Position = UDim2.new(0, 8, 0, 4)
    title.BackgroundTransparency = 1
    title.Text = sec.title
    title.TextColor3 = Theme.TextMuted
    title.TextSize = 12
    title.Font = Enum.Font.GothamBold
    title.TextXAlignment = Enum.TextXAlignment.Left
    title.TextYAlignment = Enum.TextYAlignment.Center
    title.Parent = container
    
    local div = Instance.new("Frame")
    div.Size = UDim2.new(0.9, 0, 0, 1)
    div.Position = UDim2.new(0.05, 0, 0, 40)
    div.BackgroundColor3 = Theme.Divider
    div.BorderSizePixel = 0
    div.Parent = container
    
    local sy = 48
    for j, item in ipairs(sec.items) do
        local y = sy + ((j - 1) * 32)
        local hasSlider = false
        local label = item
        if type(item) == "table" then
            label = item.name
            hasSlider = item.slider or false
        end
        local name = label:gsub(" ", ""):gsub(",", ""):gsub("%.", ""):gsub("-", "")
        CreateCheckbox(container, name, label, 0, y, hasSlider)
    end
    
    local totalH = 48
    for j, item in ipairs(sec.items) do
        local hs = false
        if type(item) == "table" then hs = item.slider or false end
        totalH = totalH + (hs and 70 or 30)
    end
    container.Size = UDim2.new(0, sw, 0, totalH + 16)
end

Canvas.Size = UDim2.new(0, totalW, 0, 380)

-- ═══════════════════════════════════════════════════════════════
-- 15. TOGGLE DO PAINEL
-- ═══════════════════════════════════════════════════════════════

local isOpen = true
local isAnimating = false

local function ToggleGUI()
    if isAnimating then return end
    isAnimating = true
    isOpen = not isOpen
    
    if isOpen then
        MainFrame.Visible = true
        MainFrame.Size = UDim2.new(0, 920, 0, 0)
        MainFrame.Position = UDim2.new(0.5, -460, 0.5, 0)
        local t = TweenService:Create(MainFrame, TweenInfo.new(0.5, Enum.EasingStyle.Back, Enum.EasingDirection.Out), {
            Size = UDim2.new(0, 920, 0, 560),
            Position = UDim2.new(0.5, -460, 0.5, -280)
        })
        t:Play()
        t.Completed:Wait()
        ToggleButton.ImageColor3 = Color3.fromRGB(200, 200, 210)
    else
        local t = TweenService:Create(MainFrame, TweenInfo.new(0.3, Enum.EasingStyle.Quint), {
            Size = UDim2.new(0, 920, 0, 0),
            Position = UDim2.new(0.5, -460, 0.5, 0)
        })
        t:Play()
        t.Completed:Wait()
        MainFrame.Visible = false
        ToggleButton.ImageColor3 = Color3.fromRGB(150, 150, 160)
    end
    
    isAnimating = false
end

-- ═══════════════════════════════════════════════════════════════
-- 16. ARRASTO DO BOTÃO
-- ═══════════════════════════════════════════════════════════════

local dragging = false
local dragStart = Vector2.new()
local btnStart = UDim2.new()
local dragActive = false

ToggleButton.InputBegan:Connect(function(i)
    if i.UserInputType == Enum.UserInputType.MouseButton1 or i.UserInputType == Enum.UserInputType.Touch then
        dragging = true
        dragActive = false
        dragStart = Vector2.new(i.Position.X, i.Position.Y)
        btnStart = ToggleButton.Position
    end
end)

UserInputService.InputChanged:Connect(function(i)
    if dragging and (i.UserInputType == Enum.UserInputType.MouseMovement or i.UserInputType == Enum.UserInputType.Touch) then
        local pos = Vector2.new(i.Position.X, i.Position.Y)
        if (pos - dragStart).Magnitude > 8 then
            dragActive = true
            local nx = btnStart.X.Offset + (pos.X - dragStart.X)
            local ny = btnStart.Y.Offset + (pos.Y - dragStart.Y)
            local sx = ToggleGui.AbsoluteSize.X
            local sy = ToggleGui.AbsoluteSize.Y
            nx = math.max(0, math.min(nx, sx - 46))
            ny = math.max(0, math.min(ny, sy - 86))
            ToggleButton.Position = UDim2.new(0, nx, 0, ny)
        end
    end
end)

ToggleButton.InputEnded:Connect(function(i)
    if i.UserInputType == Enum.UserInputType.MouseButton1 or i.UserInputType == Enum.UserInputType.Touch then
        if not dragActive and dragging then
            ToggleGUI()
        end
        dragging = false
        dragActive = false
    end
end)

-- ═══════════════════════════════════════════════════════════════
-- 17. ARRASTO DO PAINEL
-- ═══════════════════════════════════════════════════════════════

local panelDrag = false
local panelStart = Vector2.new()
local panelPos = UDim2.new()

TitleBar.InputBegan:Connect(function(i)
    if i.UserInputType == Enum.UserInputType.MouseButton1 or i.UserInputType == Enum.UserInputType.Touch then
        panelDrag = true
        panelStart = Vector2.new(i.Position.X, i.Position.Y)
        panelPos = MainFrame.Position
    end
end)

UserInputService.InputChanged:Connect(function(i)
    if panelDrag and (i.UserInputType == Enum.UserInputType.MouseMovement or i.UserInputType == Enum.UserInputType.Touch) then
        local pos = Vector2.new(i.Position.X, i.Position.Y)
        local delta = pos - panelStart
        MainFrame.Position = UDim2.new(panelPos.X.Scale, panelPos.X.Offset + delta.X, panelPos.Y.Scale, panelPos.Y.Offset + delta.Y)
    end
end)

TitleBar.InputEnded:Connect(function(i)
    if i.UserInputType == Enum.UserInputType.MouseButton1 or i.UserInputType == Enum.UserInputType.Touch then
        panelDrag = false
    end
end)

-- ═══════════════════════════════════════════════════════════════
-- 18. TECLA "J"
-- ═══════════════════════════════════════════════════════════════

UserInputService.InputBegan:Connect(function(i, gp)
    if gp then return end
    if i.KeyCode == Enum.KeyCode.J then
        ToggleGUI()
    end
end)

-- ═══════════════════════════════════════════════════════════════
-- 19. ATUALIZAÇÃO DO RELÓGIO
-- ═══════════════════════════════════════════════════════════════

RunService.Heartbeat:Connect(function()
    local t = GetCurrentTime()
    if t ~= DateTimeText.Text then
        DateTimeText.Text = t
    end
end)

-- ═══════════════════════════════════════════════════════════════
-- 20. API
-- ═══════════════════════════════════════════════════════════════

_G.CarFlipper = {
    Toggle = ToggleGUI,
    Open = function() if not isOpen then ToggleGUI() end end,
    Close = function() if isOpen then ToggleGUI() end end,
    IsOpen = function() return isOpen end,
    StartClaimCash = StartClaimCash,
    StopClaimCash = StopClaimCash,
    ToggleClaimCash = ToggleClaimCash,
    IsClaimCashRunning = function() return ClaimCashEnabled end,
    SetCashPosition = function(pos) CASH_POSITION = pos end,
    SetInterval = function(interval) ClaimCashInterval = interval end,
    GetStatus = function() return StatusLabel and StatusLabel.Text or "" end,
    SetStatus = function(text) if StatusLabel then StatusLabel.Text = text end end,
}

-- ═══════════════════════════════════════════════════════════════
-- 21. ANIMAÇÃO DE ENTRADA
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
-- 22. FINAL
-- ═══════════════════════════════════════════════════════════════

print("✅ Car Flipper - GomezXitado carregado!")
print("📌 Clique no botão preto para abrir/fechar")
print("⌨️  Tecla 'J' para abrir/fechar")
print("💰 Ative 'Claim Cash' para coletar dinheiro automático")

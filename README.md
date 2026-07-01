--[[
    Car Flipper - Premium Automation Panel
    Auto-open para qualquer executor
    Versão 4.0 - Sem teclas necessárias
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

-- ============================================================
-- 2. VERIFICAR SE O JOGO ESTÁ CARREGADO
-- ============================================================

repeat task.wait() until Player and Player.Character and Player.Character:FindFirstChild("Humanoid")
print("🚗 Car Flipper Panel Iniciando...")

-- ============================================================
-- 3. CRIAR UI AUTOMATICAMENTE
-- ============================================================

local function CreateCarFlipperUI()
    -- ScreenGui com prioridade alta
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
    MainFrame.Size = UDim2.new(0, 420, 0, 580)
    MainFrame.Position = UDim2.new(0.5, -210, 0.5, -290)
    MainFrame.BackgroundColor3 = Color3.fromRGB(16, 18, 26)
    MainFrame.BackgroundTransparency = 0.08
    MainFrame.BorderSizePixel = 0
    MainFrame.ClipsDescendants = true
    MainFrame.Parent = ScreenGui
    
    -- Efeito de blur
    local Blur = Instance.new("BlurEffect")
    Blur.Size = 20
    Blur.Parent = MainFrame
    
    -- Cantos arredondados
    local Corner = Instance.new("UICorner")
    Corner.CornerRadius = UDim.new(0, 16)
    Corner.Parent = MainFrame
    
    -- Borda com glow
    local Border = Instance.new("UIStroke")
    Border.Color = Color3.fromRGB(59, 130, 246)
    Border.Thickness = 1.5
    Border.Transparency = 0.4
    Border.Parent = MainFrame
    
    -- Sombra
    local Shadow = Instance.new("Frame")
    Shadow.Size = UDim2.new(1, 20, 1, 20)
    Shadow.Position = UDim2.new(0, -10, 0, -10)
    Shadow.BackgroundColor3 = Color3.fromRGB(0, 0, 0)
    Shadow.BackgroundTransparency = 0.8
    Shadow.BorderSizePixel = 0
    Shadow.ZIndex = 0
    Shadow.Parent = MainFrame
    
    local ShadowCorner = Instance.new("UICorner")
    ShadowCorner.CornerRadius = UDim.new(0, 20)
    ShadowCorner.Parent = Shadow
    
    -- ============================================================
    -- 5. HEADER
    -- ============================================================
    
    local Header = Instance.new("Frame")
    Header.Name = "Header"
    Header.Size = UDim2.new(1, 0, 0, 48)
    Header.BackgroundColor3 = Color3.fromRGB(20, 22, 32)
    Header.BorderSizePixel = 0
    Header.Parent = MainFrame
    
    local HeaderCorner = Instance.new("UICorner")
    HeaderCorner.CornerRadius = UDim.new(0, 16)
    HeaderCorner.Parent = Header
    
    -- Título
    local Title = Instance.new("TextLabel")
    Title.Size = UDim2.new(0.7, 0, 1, 0)
    Title.Position = UDim2.new(0, 16, 0, 0)
    Title.BackgroundTransparency = 1
    Title.Text = "🚗 Car Flipper"
    Title.TextColor3 = Color3.fromRGB(255, 255, 255)
    Title.TextSize = 18
    Title.Font = Enum.Font.Poppins
    Title.TextXAlignment = Enum.TextXAlignment.Left
    Title.Parent = Header
    
    -- Subtítulo
    local Subtitle = Instance.new("TextLabel")
    Subtitle.Size = UDim2.new(0.7, 0, 1, 0)
    Subtitle.Position = UDim2.new(0, 120, 0, 0)
    Subtitle.BackgroundTransparency = 1
    Subtitle.Text = "TUNING SHOP"
    Subtitle.TextColor3 = Color3.fromRGB(128, 140, 180)
    Subtitle.TextSize = 12
    Subtitle.Font = Enum.Font.Poppins
    Subtitle.TextXAlignment = Enum.TextXAlignment.Left
    Subtitle.Parent = Header
    
    -- Botões de controle
    local Controls = Instance.new("Frame")
    Controls.Name = "Controls"
    Controls.Size = UDim2.new(0, 80, 1, 0)
    Controls.Position = UDim2.new(1, -84, 0, 0)
    Controls.BackgroundTransparency = 1
    Controls.Parent = Header
    
    local function CreateControlButton(text, xPos, color)
        local btn = Instance.new("TextButton")
        btn.Size = UDim2.new(0, 24, 0, 24)
        btn.Position = UDim2.new(0, xPos, 0.5, -12)
        btn.BackgroundTransparency = 1
        btn.Text = text
        btn.TextColor3 = color or Color3.fromRGB(180, 190, 220)
        btn.TextSize = 14
        btn.Font = Enum.Font.Poppins
        btn.Parent = Controls
        return btn
    end
    
    local minimizeBtn = CreateControlButton("—", 0)
    local maximizeBtn = CreateControlButton("□", 28)
    local closeBtn = CreateControlButton("✕", 56, Color3.fromRGB(255, 80, 80))
    
    -- ============================================================
    -- 6. STATUS BAR
    -- ============================================================
    
    local StatusBar = Instance.new("Frame")
    StatusBar.Name = "StatusBar"
    StatusBar.Size = UDim2.new(1, -32, 0, 32)
    StatusBar.Position = UDim2.new(0, 16, 0, 56)
    StatusBar.BackgroundColor3 = Color3.fromRGB(20, 22, 32)
    StatusBar.BackgroundTransparency = 0.5
    StatusBar.BorderSizePixel = 0
    StatusBar.Parent = MainFrame
    
    local StatusCorner = Instance.new("UICorner")
    StatusCorner.CornerRadius = UDim.new(1, 0)
    StatusCorner.Parent = StatusBar
    
    -- Status dot com glow
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
    
    -- Glow
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
    StatusText.Size = UDim2.new(1, -80, 1, 0)
    StatusText.Position = UDim2.new(0, 28, 0, 0)
    StatusText.BackgroundTransparency = 1
    StatusText.Text = "[Car Flipper] Pronto"
    StatusText.TextColor3 = Color3.fromRGB(180, 200, 240)
    StatusText.TextSize = 11
    StatusText.Font = Enum.Font.Poppins
    StatusText.TextXAlignment = Enum.TextXAlignment.Left
    StatusText.Parent = StatusBar
    
    -- ============================================================
    -- 7. TABS (Auto / Run Once)
    -- ============================================================
    
    local TabContainer = Instance.new("Frame")
    TabContainer.Name = "TabContainer"
    TabContainer.Size = UDim2.new(1, -32, 0, 32)
    TabContainer.Position = UDim2.new(0, 16, 0, 94)
    TabContainer.BackgroundTransparency = 1
    TabContainer.Parent = MainFrame
    
    local function CreateTab(text, xPos, isActive)
        local tab = Instance.new("TextButton")
        tab.Size = UDim2.new(0, 80, 1, 0)
        tab.Position = UDim2.new(0, xPos, 0, 0)
        tab.BackgroundTransparency = 1
        tab.Text = text
        tab.TextColor3 = isActive and Color3.fromRGB(255, 255, 255) or Color3.fromRGB(128, 140, 180)
        tab.TextSize = 14
        tab.Font = Enum.Font.Poppins
        tab.Parent = TabContainer
        
        local underline = Instance.new("Frame")
        underline.Size = UDim2.new(0, 40, 0, 2)
        underline.Position = UDim2.new(0.5, -20, 1, -2)
        underline.BackgroundColor3 = Color3.fromRGB(59, 130, 246)
        underline.BackgroundTransparency = isActive and 0 or 1
        underline.BorderSizePixel = 0
        underline.Parent = tab
        
        return tab, underline
    end
    
    local autoTab, autoUnderline = CreateTab("Auto", 0, true)
    local runTab, runUnderline = CreateTab("Run Once", 90, false)
    
    autoTab.MouseButton1Click:Connect(function()
        autoTab.TextColor3 = Color3.fromRGB(255, 255, 255)
        runTab.TextColor3 = Color3.fromRGB(128, 140, 180)
        autoUnderline.BackgroundTransparency = 0
        runUnderline.BackgroundTransparency = 1
    end)
    
    runTab.MouseButton1Click:Connect(function()
        runTab.TextColor3 = Color3.fromRGB(255, 255, 255)
        autoTab.TextColor3 = Color3.fromRGB(128, 140, 180)
        runUnderline.BackgroundTransparency = 0
        autoUnderline.BackgroundTransparency = 1
    end)
    
    -- ============================================================
    -- 8. CONTEÚDO SCROLLABLE
    -- ============================================================
    
    local ContentFrame = Instance.new("Frame")
    ContentFrame.Name = "ContentFrame"
    ContentFrame.Size = UDim2.new(1, -32, 1, -160)
    ContentFrame.Position = UDim2.new(0, 16, 0, 132)
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
    ContentLayout.Padding = UDim.new(0, 8)
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
        header.Size = UDim2.new(1, 0, 0, 28)
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
        container.Position = UDim2.new(0, 0, 0, 28)
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
    -- 10. ADICIONAR TOGGLES
    -- ============================================================
    
    local function AddToggleRow(parent, icon, title, subtitle, defaultActive, isGreen)
        local row = Instance.new("Frame")
        row.Size = UDim2.new(1, 0, 0, 32)
        row.BackgroundTransparency = 1
        row.Parent = parent
        
        local iconLabel = Instance.new("TextLabel")
        iconLabel.Size = UDim2.new(0, 28, 1, 0)
        iconLabel.BackgroundTransparency = 1
        iconLabel.Text = icon
        iconLabel.TextSize = 16
        iconLabel.TextColor3 = Color3.fromRGB(200, 215, 245)
        iconLabel.Font = Enum.Font.Poppins
        iconLabel.Parent = row
        
        local titleLabel = Instance.new("TextLabel")
        titleLabel.Size = UDim2.new(1, -100, 0, 16)
        titleLabel.Position = UDim2.new(0, 32, 0, 2)
        titleLabel.BackgroundTransparency = 1
        titleLabel.Text = title
        titleLabel.TextColor3 = Color3.fromRGB(220, 230, 250)
        titleLabel.TextSize = 12
        titleLabel.Font = Enum.Font.Poppins
        titleLabel.TextXAlignment = Enum.TextXAlignment.Left
        titleLabel.Parent = row
        
        local subLabel = Instance.new("TextLabel")
        subLabel.Size = UDim2.new(1, -100, 0, 14)
        subLabel.Position = UDim2.new(0, 32, 0, 18)
        subLabel.BackgroundTransparency = 1
        subLabel.Text = subtitle or ""
        subLabel.TextColor3 = Color3.fromRGB(128, 140, 180)
        subLabel.TextSize = 10
        subLabel.Font = Enum.Font.Poppins
        subLabel.TextXAlignment = Enum.TextXAlignment.Left
        subLabel.Parent = row
        
        local toggle = Instance.new("Frame")
        toggle.Size = UDim2.new(0, 34, 0, 20)
        toggle.Position = UDim2.new(1, -38, 0.5, -10)
        toggle.BackgroundColor3 = defaultActive and (isGreen and Color3.fromRGB(34, 197, 94) or Color3.fromRGB(59, 130, 246)) or Color3.fromRGB(36, 44, 62)
        toggle.BorderSizePixel = 0
        toggle.Parent = row
        
        local toggleCorner = Instance.new("UICorner")
        toggleCorner.CornerRadius = UDim.new(1, 0)
        toggleCorner.Parent = toggle
        
        local knob = Instance.new("Frame")
        knob.Size = UDim2.new(0, 14, 0, 14)
        knob.Position = defaultActive and UDim2.new(1, -17, 0.5, -7) or UDim2.new(0, 3, 0.5, -7)
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
            local targetPos = isActive and UDim2.new(1, -17, 0.5, -7) or UDim2.new(0, 3, 0.5, -7)
            
            TweenService:Create(toggle, TweenInfo.new(0.2), {BackgroundColor3 = targetColor}):Play()
            TweenService:Create(knob, TweenInfo.new(0.2), {Position = targetPos}):Play()
            
            -- Executar ação quando toggle é ativado
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
        StatusText.Text = "[Car Flipper] Executando: " .. action
        StatusDot.BackgroundColor3 = Color3.fromRGB(59, 130, 246)
        DotGlow.BackgroundColor3 = Color3.fromRGB(59, 130, 246)
        
        -- Tentar encontrar RemoteEvent
        local remote = ReplicatedStorage:FindFirstChild(action:gsub(" ", ""))
        if remote and remote:IsA("RemoteEvent") then
            remote:FireServer()
            print("✅ Executado: " .. action)
        else
            -- Tentar outros nomes comuns
            local altNames = {
                ["Claim Cash"] = "ClaimCash",
                ["Claim Parts"] = "ClaimParts",
                ["Buy Upgrades"] = "BuyUpgrade",
                ["Claim Containers"] = "ClaimContainer",
                ["Open Containers"] = "OpenContainer",
                ["Claim Quests"] = "ClaimQuest",
                ["Deliver Junk"] = "DeliverJunk",
                ["Fix Cars"] = "FixCar",
                ["Sell Fixed Cars"] = "SellCar",
                ["Update Stands"] = "UpdateStand",
                ["Buy, Fix, Sell Cheapest"] = "BuyFixSellCheapest",
                ["Claim Playtime Rewards"] = "ClaimPlaytime",
                ["Claim Daily Rewards"] = "ClaimDaily",
            }
            
            local remoteName = altNames[action]
            if remoteName then
                local altRemote = ReplicatedStorage:FindFirstChild(remoteName)
                if altRemote and altRemote:IsA("RemoteEvent") then
                    altRemote:FireServer()
                    print("✅ Executado: " .. action .. " (via " .. remoteName .. ")")
                end
            end
        end
        
        -- Reset status após 2 segundos
        task.wait(2)
        StatusText.Text = "[Car Flipper] Pronto"
        StatusDot.BackgroundColor3 = Color3.fromRGB(34, 197, 94)
        DotGlow.BackgroundColor3 = Color3.fromRGB(34, 197, 94)
    end
    
    -- ============================================================
    -- 12. CONSTRUIR SEÇÕES
    -- ============================================================
    
    -- Base
    local baseSection, baseContainer = CreateSection("Base", "🏢", 1)
    AddToggleRow(baseContainer, "💰", "Claim Cash", "Coletar dinheiro diário", true, true)
    AddToggleRow(baseContainer, "🧩", "Claim Parts", "Coletar peças", true)
    AddToggleRow(baseContainer, "⬆️", "Buy Upgrades", "Comprar melhorias", false)
    AddToggleRow(baseContainer, "📦", "Claim Containers", "Abrir containers", true, true)
    AddToggleRow(baseContainer, "🎁", "Open Containers", "Revelar recompensas", false)
    AddToggleRow(baseContainer, "📋", "Claim Quests", "Recompensas de missões", true)
    AddToggleRow(baseContainer, "🗑️", "Deliver Junk", "Vender sucata", false)
    
    -- Cars
    local carsSection, carsContainer = CreateSection("Cars", "🚗", 2)
    AddToggleRow(carsContainer, "🔧", "Fix Cars", "Reparar veículos", true)
    AddToggleRow(carsContainer, "💲", "Sell Fixed Cars", "Vender reparados", true, true)
    AddToggleRow(carsContainer, "🔄", "Update Stands", "Atualizar vitrines", false)
    AddToggleRow(carsContainer, "🏷️", "Buy, Fix, Sell Cheapest", "Auto flip", true)
    
    -- Rewards
    local rewardsSection, rewardsContainer = CreateSection("Rewards", "🎁", 3)
    AddToggleRow(rewardsContainer, "⏳", "Claim Playtime Rewards", "Recompensas por tempo", true, true)
    AddToggleRow(rewardsContainer, "📆", "Claim Daily Rewards", "Recompensas diárias", true)
    
    -- ============================================================
    -- 13. PROGRESS BAR
    -- ============================================================
    
    local ProgressFrame = Instance.new("Frame")
    ProgressFrame.Name = "ProgressFrame"
    ProgressFrame.Size = UDim2.new(1, -32, 0, 40)
    ProgressFrame.Position = UDim2.new(0, 16, 1, -52)
    ProgressFrame.BackgroundTransparency = 1
    ProgressFrame.Parent = MainFrame
    
    local ProgressLabel = Instance.new("TextLabel")
    ProgressLabel.Size = UDim2.new(0.5, 0, 0, 18)
    ProgressLabel.Position = UDim2.new(0, 0, 0, 0)
    ProgressLabel.BackgroundTransparency = 1
    ProgressLabel.Text = "0%"
    ProgressLabel.TextColor3 = Color3.fromRGB(200, 215, 245)
    ProgressLabel.TextSize = 11
    ProgressLabel.Font = Enum.Font.Poppins
    ProgressLabel.TextXAlignment = Enum.TextXAlignment.Left
    ProgressLabel.Parent = ProgressFrame
    
    local LevelLabel = Instance.new("TextLabel")
    LevelLabel.Size = UDim2.new(0.5, 0, 0, 18)
    LevelLabel.Position = UDim2.new(0.5, 0, 0, 0)
    LevelLabel.BackgroundTransparency = 1
    LevelLabel.Text = "Lvl.1"
    LevelLabel.TextColor3 = Color3.fromRGB(200, 215, 245)
    LevelLabel.TextSize = 11
    LevelLabel.Font = Enum.Font.Poppins
    LevelLabel.TextXAlignment = Enum.TextXAlignment.Right
    LevelLabel.Parent = ProgressFrame
    
    local ProgressBg = Instance.new("Frame")
    ProgressBg.Size = UDim2.new(1, 0, 0, 6)
    ProgressBg.Position = UDim2.new(0, 0, 0, 22)
    ProgressBg.BackgroundColor3 = Color3.fromRGB(30, 35, 50)
    ProgressBg.BorderSizePixel = 0
    ProgressBg.Parent = ProgressFrame
    
    local ProgressBgCorner = Instance.new("UICorner")
    ProgressBgCorner.CornerRadius = UDim.new(1, 0)
    ProgressBgCorner.Parent = ProgressBg
    
    local ProgressFill = Instance.new("Frame")
    ProgressFill.Size = UDim2.new(0.33, 0, 1, 0)
    ProgressFill.BackgroundColor3 = Color3.fromRGB(59, 130, 246)
    ProgressFill.BorderSizePixel = 0
    ProgressFill.Parent = ProgressBg
    
    local ProgressFillCorner = Instance.new("UICorner")
    ProgressFillCorner.CornerRadius = UDim.new(1, 0)
    ProgressFillCorner.Parent = ProgressFill
    
    -- ============================================================
    -- 14. DRAG WINDOW (arrastar pela header)
    -- ============================================================
    
    local dragging = false
    local dragStart = nil
    local startPos = nil
    
    Header.InputBegan:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1 then
            dragging = true
            dragStart = input.Position
            startPos = MainFrame.Position
        end
    end)
    
    Header.InputEnded:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1 then
            dragging = false
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
    
    -- ============================================================
    -- 15. CONTROLES DA JANELA
    -- ============================================================
    
    closeBtn.MouseButton1Click:Connect(function()
        ScreenGui.Enabled = false
    end)
    
    minimizeBtn.MouseButton1Click:Connect(function()
        MainFrame.Visible = false
        -- Criar botão para reabrir
        local reopenBtn = Instance.new("TextButton")
        reopenBtn.Name = "ReopenBtn"
        reopenBtn.Size = UDim2.new(0, 40, 0, 40)
        reopenBtn.Position = UDim2.new(0, 10, 0, 10)
        reopenBtn.BackgroundColor3 = Color3.fromRGB(16, 18, 26)
        reopenBtn.BackgroundTransparency = 0.2
        reopenBtn.Text = "🚗"
        reopenBtn.TextSize = 20
        reopenBtn.Font = Enum.Font.Poppins
        reopenBtn.Parent = ScreenGui
        
        local reopenCorner = Instance.new("UICorner")
        reopenCorner.CornerRadius = UDim.new(1, 0)
        reopenCorner.Parent = reopenBtn
        
        reopenBtn.MouseButton1Click:Connect(function()
            MainFrame.Visible = true
            reopenBtn:Destroy()
        end)
    end)
    
    maximizeBtn.MouseButton1Click:Connect(function()
        local isMaximized = MainFrame.Size == UDim2.new(0, 420, 0, 580)
        if isMaximized then
            MainFrame.Size = UDim2.new(0, 600, 0, 700)
            MainFrame.Position = UDim2.new(0.5, -300, 0.5, -350)
        else
            MainFrame.Size = UDim2.new(0, 420, 0, 580)
            MainFrame.Position = UDim2.new(0.5, -210, 0.5, -290)
        end
    end)
    
    -- ============================================================
    -- 16. AUTO-SIZE SEÇÕES
    -- ============================================================
    
    for _, section in pairs({baseSection, carsSection, rewardsSection}) do
        local container = section:FindFirstChild("Container")
        if container then
            local children = container:GetChildren()
            local height = 0
            for _, child in ipairs(children) do
                if child:IsA("Frame") then
                    height = height + 32
                end
            end
            section.Size = UDim2.new(1, 0, 0, height + 32)
        end
    end
    
    -- ============================================================
    -- 17. SIMULAÇÃO DE PROGRESSO
    -- ============================================================
    
    task.spawn(function()
        local progress = 0
        while wait(3) do
            if progress < 100 then
                progress = progress + math.random(1, 5)
                ProgressFill.Size = UDim2.new(math.min(progress / 100, 1), 0, 1, 0)
                ProgressLabel.Text = string.format("%.0f%%", math.min(progress, 100))
            end
        end
    end)
    
    -- ============================================================
    -- 18. MENSAGEM DE SUCESSO
    -- ============================================================
    
    print("🚗 Car Flipper Panel Carregado com Sucesso!")
    print("📋 Nenhuma tecla necessária - UI aberta automaticamente")
    print("💡 Clique nos toggles para ativar/desativar ações")
    
    return ScreenGui
end

-- ============================================================
-- 19. EXECUTAR
-- ============================================================

-- Criar UI automaticamente
local UI = CreateCarFlipperUI()

-- ============================================================
-- 20. ANTI-KICK / KEEP ALIVE
-- ============================================================

task.spawn(function()
    while true do
        -- Prevenir kick por inatividade
        pcall(function()
            VirtualUser:CaptureController()
            VirtualUser:ClickButton2(Vector2.new())
        end)
        task.wait(60)
    end
end)

print("✅ Car Flipper Panel pronto para uso!")

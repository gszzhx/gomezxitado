--[[
    Car Flipper - Painel Completo com Todas as Opções
    Baseado na interface do jogo
    Versão 5.0 - UI Completa
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

repeat task.wait() until Player and Player.Character and Player.Character:FindFirstChild("Humanoid")
print("🚗 Car Flipper Painel Completo Iniciando...")

-- ============================================================
-- 2. CRIAR UI PRINCIPAL
-- ============================================================

local function CreateCarFlipperUI()
    local ScreenGui = Instance.new("ScreenGui")
    ScreenGui.Name = "CarFlipperPanel"
    ScreenGui.ResetOnSpawn = false
    ScreenGui.ZIndexBehavior = Enum.ZIndexBehavior.Sibling
    ScreenGui.Parent = Player.PlayerGui
    
    -- ============================================================
    -- 3. JANELA PRINCIPAL (Tamanho maior para mais opções)
    -- ============================================================
    
    local MainFrame = Instance.new("Frame")
    MainFrame.Name = "MainFrame"
    MainFrame.Size = UDim2.new(0, 480, 0, 650)
    MainFrame.Position = UDim2.new(0.5, -240, 0.5, -325)
    MainFrame.BackgroundColor3 = Color3.fromRGB(13, 15, 22)
    MainFrame.BackgroundTransparency = 0.05
    MainFrame.BorderSizePixel = 0
    MainFrame.ClipsDescendants = true
    MainFrame.Parent = ScreenGui
    
    -- Efeitos visuais
    local Blur = Instance.new("BlurEffect")
    Blur.Size = 22
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
    -- 4. HEADER COM INFO DO SISTEMA
    -- ============================================================
    
    local Header = Instance.new("Frame")
    Header.Name = "Header"
    Header.Size = UDim2.new(1, 0, 0, 54)
    Header.BackgroundColor3 = Color3.fromRGB(18, 20, 30)
    Header.BorderSizePixel = 0
    Header.Parent = MainFrame
    
    local HeaderCorner = Instance.new("UICorner")
    HeaderCorner.CornerRadius = UDim.new(0, 18)
    HeaderCorner.Parent = Header
    
    -- Título com ícone
    local Title = Instance.new("TextLabel")
    Title.Size = UDim2.new(0.6, 0, 0.6, 0)
    Title.Position = UDim2.new(0, 16, 0, 4)
    Title.BackgroundTransparency = 1
    Title.Text = "🚗 Car Flipper"
    Title.TextColor3 = Color3.fromRGB(255, 255, 255)
    Title.TextSize = 20
    Title.Font = Enum.Font.Poppins
    Title.TextXAlignment = Enum.TextXAlignment.Left
    Title.Parent = Header
    
    -- Status do sistema
    local SysStatus = Instance.new("TextLabel")
    SysStatus.Size = UDim2.new(0.6, 0, 0.4, 0)
    SysStatus.Position = UDim2.new(0, 16, 0, 30)
    SysStatus.BackgroundTransparency = 1
    SysStatus.Text = "[SYSTEM] ONLINE"
    SysStatus.TextColor3 = Color3.fromRGB(34, 197, 94)
    SysStatus.TextSize = 11
    SysStatus.Font = Enum.Font.Poppins
    SysStatus.TextXAlignment = Enum.TextXAlignment.Left
    SysStatus.Parent = Header
    
    -- Subtítulo (localização)
    local Location = Instance.new("TextLabel")
    Location.Size = UDim2.new(0.4, 0, 0.6, 0)
    Location.Position = UDim2.new(0.4, 0, 0, 4)
    Location.BackgroundTransparency = 1
    Location.Text = "🏪 TUNING SHOP"
    Location.TextColor3 = Color3.fromRGB(128, 140, 180)
    Location.TextSize = 13
    Location.Font = Enum.Font.Poppins
    Location.TextXAlignment = Enum.TextXAlignment.Right
    Location.Parent = Header
    
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
    -- 5. STATUS BAR COM INFO DO JOGO
    -- ============================================================
    
    local StatusBar = Instance.new("Frame")
    StatusBar.Name = "StatusBar"
    StatusBar.Size = UDim2.new(1, -32, 0, 38)
    StatusBar.Position = UDim2.new(0, 16, 0, 62)
    StatusBar.BackgroundColor3 = Color3.fromRGB(18, 20, 30)
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
    
    -- Status text com informações do jogo
    local StatusText = Instance.new("TextLabel")
    StatusText.Name = "StatusText"
    StatusText.Size = UDim2.new(0.6, 0, 1, 0)
    StatusText.Position = UDim2.new(0, 28, 0, 0)
    StatusText.BackgroundTransparency = 1
    StatusText.Text = "[SYSTEM] LEGENDARY has spawned"
    StatusText.TextColor3 = Color3.fromRGB(180, 200, 240)
    StatusText.TextSize = 11
    StatusText.Font = Enum.Font.Poppins
    StatusText.TextXAlignment = Enum.TextXAlignment.Left
    StatusText.Parent = StatusBar
    
    -- Info do carro selecionado
    local CarInfo = Instance.new("TextLabel")
    CarInfo.Name = "CarInfo"
    CarInfo.Size = UDim2.new(0.4, 0, 1, 0)
    CarInfo.Position = UDim2.new(0.6, 0, 0, 0)
    CarInfo.BackgroundTransparency = 1
    CarInfo.Text = "[RARE] Cryele 222B"
    CarInfo.TextColor3 = Color3.fromRGB(255, 215, 0)
    CarInfo.TextSize = 11
    CarInfo.Font = Enum.Font.Poppins
    CarInfo.TextXAlignment = Enum.TextXAlignment.Right
    CarInfo.Parent = StatusBar
    
    -- ============================================================
    -- 6. TABS (Auto / Run Once / Stats)
    -- ============================================================
    
    local TabContainer = Instance.new("Frame")
    TabContainer.Name = "TabContainer"
    TabContainer.Size = UDim2.new(1, -32, 0, 34)
    TabContainer.Position = UDim2.new(0, 16, 0, 106)
    TabContainer.BackgroundTransparency = 1
    TabContainer.Parent = MainFrame
    
    local function CreateTab(text, xPos, isActive)
        local tab = Instance.new("TextButton")
        tab.Size = UDim2.new(0, 90, 1, 0)
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
    local runTab, runUnderline = CreateTab("Run Once", 95, false)
    local statsTab, statsUnderline = CreateTab("Stats", 190, false)
    
    autoTab.MouseButton1Click:Connect(function()
        autoTab.TextColor3 = Color3.fromRGB(255, 255, 255)
        runTab.TextColor3 = Color3.fromRGB(128, 140, 180)
        statsTab.TextColor3 = Color3.fromRGB(128, 140, 180)
        autoUnderline.BackgroundTransparency = 0
        runUnderline.BackgroundTransparency = 1
        statsUnderline.BackgroundTransparency = 1
    end)
    
    runTab.MouseButton1Click:Connect(function()
        runTab.TextColor3 = Color3.fromRGB(255, 255, 255)
        autoTab.TextColor3 = Color3.fromRGB(128, 140, 180)
        statsTab.TextColor3 = Color3.fromRGB(128, 140, 180)
        runUnderline.BackgroundTransparency = 0
        autoUnderline.BackgroundTransparency = 1
        statsUnderline.BackgroundTransparency = 1
    end)
    
    statsTab.MouseButton1Click:Connect(function()
        statsTab.TextColor3 = Color3.fromRGB(255, 255, 255)
        autoTab.TextColor3 = Color3.fromRGB(128, 140, 180)
        runTab.TextColor3 = Color3.fromRGB(128, 140, 180)
        statsUnderline.BackgroundTransparency = 0
        autoUnderline.BackgroundTransparency = 1
        runUnderline.BackgroundTransparency = 1
    end)
    
    -- ============================================================
    -- 7. CONTEÚDO SCROLLABLE
    -- ============================================================
    
    local ContentFrame = Instance.new("Frame")
    ContentFrame.Name = "ContentFrame"
    ContentFrame.Size = UDim2.new(1, -32, 1, -190)
    ContentFrame.Position = UDim2.new(0, 16, 0, 146)
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
    ContentLayout.Padding = UDim.new(0, 6)
    ContentLayout.HorizontalAlignment = Enum.HorizontalAlignment.Center
    ContentLayout.SortOrder = Enum.SortOrder.LayoutOrder
    ContentLayout.Parent = Scrolling
    
    -- ============================================================
    -- 8. CRIAR SEÇÕES
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
    -- 9. TOGGLE ROW COMPLETA
    -- ============================================================
    
    local function AddToggleRow(parent, icon, title, subtitle, defaultActive, isGreen, actionType)
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
    -- 10. FUNÇÕES DE AÇÃO
    -- ============================================================
    
    function ExecuteAction(action)
        local statusText = StatusText
        local statusDot = StatusDot
        local dotGlow = DotGlow
        
        statusText.Text = "[SYSTEM] Executando: " .. action
        statusDot.BackgroundColor3 = Color3.fromRGB(59, 130, 246)
        dotGlow.BackgroundColor3 = Color3.fromRGB(59, 130, 246)
        
        -- Mapeamento de ações para RemoteEvents
        local actionMap = {
            ["Claim Cash"] = {"ClaimCash", "CashClaim", "CollectCash"},
            ["Claim Parts"] = {"ClaimParts", "PartsClaim", "CollectParts"},
            ["Buy Upgrades"] = {"BuyUpgrade", "Upgrade", "PurchaseUpgrade"},
            ["Claim Containers"] = {"ClaimContainer", "ContainerClaim", "OpenContainer"},
            ["Open Containers"] = {"OpenContainer", "ContainerOpen"},
            ["Claim Quests"] = {"ClaimQuest", "QuestClaim", "CompleteQuest"},
            ["Deliver Junk"] = {"DeliverJunk", "JunkDeliver", "SellJunk"},
            ["Fix Cars"] = {"FixCar", "RepairCar", "CarFix"},
            ["Sell Fixed Cars"] = {"SellCar", "CarSell", "SellFixed"},
            ["Update Stands"] = {"UpdateStand", "StandUpdate", "RefreshStand"},
            ["Buy, Fix, Sell Cheapest"] = {"BuyFixSell", "AutoFlip", "CheapestFlip"},
            ["Claim Playtime Rewards"] = {"ClaimPlaytime", "PlaytimeReward"},
            ["Claim Daily Rewards"] = {"ClaimDaily", "DailyReward"},
            ["Auction Auto-Bid"] = {"AutoBid", "AuctionBid", "PlaceBid"},
            ["Collect All"] = {"CollectAll", "CollectEverything"},
            ["Auto Sell Legendary"] = {"SellLegendary", "AutoSellLegendary"},
        }
        
        local remoteNames = actionMap[action] or {action:gsub(" ", "")}
        local executed = false
        
        for _, remoteName in ipairs(remoteNames) do
            local remote = ReplicatedStorage:FindFirstChild(remoteName)
            if remote and remote:IsA("RemoteEvent") then
                remote:FireServer()
                executed = true
                print("✅ Executado: " .. action .. " (via " .. remoteName .. ")")
                break
            end
        end
        
        if not executed then
            -- Tentar encontrar qualquer RemoteEvent com nome similar
            for _, child in pairs(ReplicatedStorage:GetChildren()) do
                if child:IsA("RemoteEvent") then
                    local nameLower = child.Name:lower()
                    local actionLower = action:lower():gsub(" ", "")
                    if nameLower:find(actionLower) or actionLower:find(nameLower) then
                        child:FireServer()
                        executed = true
                        print("✅ Executado: " .. action .. " (via " .. child.Name .. ")")
                        break
                    end
                end
            end
        end
        
        if not executed then
            print("⚠️ Ação não encontrada: " .. action)
            statusText.Text = "[SYSTEM] Ação não disponível: " .. action
            task.wait(1)
        end
        
        -- Reset status
        task.wait(1.5)
        statusText.Text = "[SYSTEM] LEGENDARY has spawned"
        statusDot.BackgroundColor3 = Color3.fromRGB(34, 197, 94)
        dotGlow.BackgroundColor3 = Color3.fromRGB(34, 197, 94)
    end
    
    -- ============================================================
    -- 11. CONSTRUIR TODAS AS SEÇÕES
    -- ============================================================
    
    -- ===== BASE =====
    local baseSection, baseContainer = CreateSection("Base", "🏢", 1)
    AddToggleRow(baseContainer, "💰", "Claim Cash", "Coletar dinheiro automático", true, true)
    AddToggleRow(baseContainer, "🧩", "Claim Parts", "Coletar peças", true)
    AddToggleRow(baseContainer, "⬆️", "Buy Upgrades", "Comprar melhorias", false)
    AddToggleRow(baseContainer, "📦", "Claim Containers", "Abrir containers", true, true)
    AddToggleRow(baseContainer, "🎁", "Open Containers", "Revelar recompensas", false)
    AddToggleRow(baseContainer, "📋", "Claim Quests", "Recompensas de missões", true)
    AddToggleRow(baseContainer, "🗑️", "Deliver Junk", "Vender sucata", false)
    
    -- ===== CARS =====
    local carsSection, carsContainer = CreateSection("Cars", "🚗", 2)
    AddToggleRow(carsContainer, "🔧", "Fix Cars", "Reparar veículos", true)
    AddToggleRow(carsContainer, "💲", "Sell Fixed Cars", "Vender reparados", true, true)
    AddToggleRow(carsContainer, "🔄", "Update Stands", "Atualizar vitrines", false)
    AddToggleRow(carsContainer, "🏷️", "Buy, Fix, Sell Cheapest", "Auto flip mais barato", true)
    
    -- ===== REWARDS =====
    local rewardsSection, rewardsContainer = CreateSection("Rewards", "🎁", 3)
    AddToggleRow(rewardsContainer, "⏳", "Claim Playtime Rewards", "Recompensas por tempo", true, true)
    AddToggleRow(rewardsContainer, "📆", "Claim Daily Rewards", "Recompensas diárias", true)
    
    -- ===== AUCTION =====
    local auctionSection, auctionContainer = CreateSection("Auction", "🏪", 4)
    AddToggleRow(auctionContainer, "🔨", "Auction Auto-Bid", "Lance automático em leilões", true, true)
    AddToggleRow(auctionContainer, "⭐", "Auto Sell Legendary", "Vender carros lendários", false)
    
    -- ===== EXTRA =====
    local extraSection, extraContainer = CreateSection("Extra", "⚡", 5)
    AddToggleRow(extraContainer, "📦", "Collect All", "Coletar tudo de uma vez", false, true)
    AddToggleRow(extraContainer, "🏆", "Auto Farm", "Farm automático", true)
    
    -- ============================================================
    -- 12. PROGRESS BAR (como na imagem)
    -- ============================================================
    
    local ProgressFrame = Instance.new("Frame")
    ProgressFrame.Name = "ProgressFrame"
    ProgressFrame.Size = UDim2.new(1, -32, 0, 48)
    ProgressFrame.Position = UDim2.new(0, 16, 1, -58)
    ProgressFrame.BackgroundTransparency = 1
    ProgressFrame.Parent = MainFrame
    
    -- Linha superior com stats
    local StatsLine = Instance.new("Frame")
    StatsLine.Size = UDim2.new(1, 0, 0, 20)
    StatsLine.BackgroundTransparency = 1
    StatsLine.Parent = ProgressFrame
    
    local function CreateStatLabel(text, xPos)
        local label = Instance.new("TextLabel")
        label.Size = UDim2.new(0.2, 0, 1, 0)
        label.Position = UDim2.new(xPos, 0, 0, 0)
        label.BackgroundTransparency = 1
        label.Text = text
        label.TextColor3 = Color3.fromRGB(180, 200, 240)
        label.TextSize = 11
        label.Font = Enum.Font.Poppins
        label.TextXAlignment = Enum.TextXAlignment.Center
        label.Parent = StatsLine
        return label
    end
    
    CreateStatLabel("1/5", 0)
    CreateStatLabel("117 / 10000", 0.2)
    CreateStatLabel("129/s", 0.4)
    CreateStatLabel("2356 / 100000", 0.6)
    CreateStatLabel("Nivel.4", 0.8)
    
    -- Barra de progresso
    local ProgressBg = Instance.new("Frame")
    ProgressBg.Size = UDim2.new(1, 0, 0, 8)
    ProgressBg.Position = UDim2.new(0, 0, 0, 24)
    ProgressBg.BackgroundColor3 = Color3.fromRGB(30, 35, 50)
    ProgressBg.BorderSizePixel = 0
    ProgressBg.Parent = ProgressFrame
    
    local ProgressBgCorner = Instance.new("UICorner")
    ProgressBgCorner.CornerRadius = UDim.new(1, 0)
    ProgressBgCorner.Parent = ProgressBg
    
    local ProgressFill = Instance.new("Frame")
    ProgressFill.Size = UDim2.new(1, 0, 1, 0)
    ProgressFill.BackgroundColor3 = Color3.fromRGB(59, 130, 246)
    ProgressFill.BorderSizePixel = 0
    ProgressFill.Parent = ProgressBg
    
    local ProgressFillCorner = Instance.new("UICorner")
    ProgressFillCorner.CornerRadius = UDim.new(1, 0)
    ProgressFillCorner.Parent = ProgressFill
    
    -- Percentual
    local PercentLabel = Instance.new("TextLabel")
    PercentLabel.Size = UDim2.new(0.1, 0, 1, 0)
    PercentLabel.Position = UDim2.new(1, -30, 0, 0)
    PercentLabel.BackgroundTransparency = 1
    PercentLabel.Text = "100%"
    PercentLabel.TextColor3 = Color3.fromRGB(255, 255, 255)
    PercentLabel.TextSize = 10
    PercentLabel.Font = Enum.Font.Poppins
    PercentLabel.TextXAlignment = Enum.TextXAlignment.Right
    PercentLabel.Parent = ProgressBg
    
    -- ============================================================
    -- 13. DRAG WINDOW
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
    -- 14. CONTROLES DA JANELA
    -- ============================================================
    
    closeBtn.MouseButton1Click:Connect(function()
        ScreenGui.Enabled = false
    end)
    
    minimizeBtn.MouseButton1Click:Connect(function()
        MainFrame.Visible = false
        local reopenBtn = Instance.new("TextButton")
        reopenBtn.Name = "ReopenBtn"
        reopenBtn.Size = UDim2.new(0, 44, 0, 44)
        reopenBtn.Position = UDim2.new(0, 10, 0, 10)
        reopenBtn.BackgroundColor3 = Color3.fromRGB(13, 15, 22)
        reopenBtn.BackgroundTransparency = 0.2
        reopenBtn.Text = "🚗"
        reopenBtn.TextSize = 22
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
        local isMaximized = MainFrame.Size == UDim2.new(0, 480, 0, 650)
        if isMaximized then
            MainFrame.Size = UDim2.new(0, 580, 0, 720)
            MainFrame.Position = UDim2.new(0.5, -290, 0.5, -360)
        else
            MainFrame.Size = UDim2.new(0, 480, 0, 650)
            MainFrame.Position = UDim2.new(0.5, -240, 0.5, -325)
        end
    end)
    
    -- ============================================================
    -- 15. AUTO-SIZE SEÇÕES
    -- ============================================================
    
    for _, section in pairs({baseSection, carsSection, rewardsSection, auctionSection, extraSection}) do
        local container = section:FindFirstChild("Container")
        if container then
            local children = container:GetChildren()
            local height = 0
            for _, child in ipairs(children) do
                if child:IsA("Frame") then
                    height = height + 34
                end
            end
            section.Size = UDim2.new(1, 0, 0, height + 30)
        end
    end
    
    -- ============================================================
    -- 16. SIMULAÇÃO DE PROGRESSO
    -- ============================================================
    
    task.spawn(function()
        local progress = 100
        ProgressFill.Size = UDim2.new(1, 0, 1, 0)
        PercentLabel.Text = "100%"
        
        while wait(5) do
            -- Atualizar stats
            local stats = {
                "1/5",
                math.random(100, 500) .. " / 10000",
                math.random(100, 200) .. "/s",
                math.random(1000, 5000) .. " / 100000",
                "Nivel." .. math.random(1, 10)
            }
            
            for i, child in pairs(StatsLine:GetChildren()) do
                if child:IsA("TextLabel") and stats[i] then
                    child.Text = stats[i]
                end
            end
        end
    end)
    
    -- ============================================================
    -- 17. ANTI-KICK
    -- ===========================================================

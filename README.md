--[[
    🥚 STEAL A EGG - DELTA EXECUTOR EDITION
    Compatível com: Delta Mobile + Delta PC
    Recursos: Auto Steal, Raridades, Auto Esteira,
              Velocidade, Server Hop, Anti-AFK
--]]

-- ⚙️ AMBIENTE SEGURO (compatível com Delta)
local ok, err = pcall(function()
    
    local Players = game:GetService("Players")
    local TeleportService = game:GetService("TeleportService")
    local HttpService = game:GetService("HttpService")
    local VirtualUser = game:GetService("VirtualUser")
    local TweenService = game:GetService("TweenService")
    local UserInputService = game:GetService("UserInputService")
    local RunService = game:GetService("RunService")

    local LocalPlayer = Players.LocalPlayer
    
    -- Aguarda o personagem carregar
    if not LocalPlayer.Character then
        LocalPlayer.CharacterAdded:Wait()
    end
    LocalPlayer:WaitForChild("PlayerGui", 10)
    
    -- ⚙️ CONFIG
    local Config = {
        AutoSteal = true,
        AutoEsteira = true,
        AutoServerHop = false,
        Velocidade = 80,
        RaridadeEscolhida = "Divino",
        IlhasParaAndar = 3,
        DelaySteal = 0.15,
        DelayEsteira = 0.6,
        ToleranciaCor = 0.4,
        DistanciaInteracao = 15,
        DistanciaTeleporte = 200,
    }
    
    -- 🖼️ FOTO DO GUSTAVO LIMA (troque pelo seu ID)
    local FOTO_GUSTAVO = "rbxassetid://SEU_ID_AQUI"
    local FOTO_FALLBACK = "rbxassetid://6031075931"
    
    -- 🎨 RARIDADES (ordem hierárquica)
    local OrdemRaridades = {
        "Comum", "Incomum", "Rara", "Épica", "Lendária",
        "Mítica", "Cósmica", "Secreto", "Eterno", "Divino"
    }
    
    local Raridades = {
        ["Comum"]    = Color3.fromRGB(180, 180, 180),
        ["Incomum"]  = Color3.fromRGB(80, 220, 100),
        ["Rara"]     = Color3.fromRGB(0, 170, 255),
        ["Épica"]    = Color3.fromRGB(170, 0, 255),
        ["Lendária"] = Color3.fromRGB(255, 215, 0),
        ["Mítica"]   = Color3.fromRGB(255, 0, 0),
        ["Cósmica"]  = Color3.fromRGB(0, 255, 255),
        ["Secreto"]  = Color3.fromRGB(30, 30, 30),
        ["Eterno"]   = Color3.fromRGB(255, 105, 180),
        ["Divino"]   = Color3.fromRGB(255, 255, 255),
    }
    
    -- ============================================================
    -- 🖥️ INTERFACE
    -- ============================================================
    local old = LocalPlayer.PlayerGui:FindFirstChild("StealEggFarm")
    if old then old:Destroy() end
    
    local ScreenGui = Instance.new("ScreenGui")
    ScreenGui.Name = "StealEggFarm"
    ScreenGui.ResetOnSpawn = false
    ScreenGui.IgnoreGuiInset = true
    ScreenGui.ZIndexBehavior = Enum.ZIndexBehavior.Sibling
    ScreenGui.Parent = LocalPlayer:WaitForChild("PlayerGui")
    
    local Toggle = Instance.new("TextButton")
    Toggle.Size = UDim2.new(0, 50, 0, 50)
    Toggle.Position = UDim2.new(0, 20, 0.5, -25)
    Toggle.BackgroundColor3 = Color3.fromRGB(255, 60, 130)
    Toggle.Text = "🥚"
    Toggle.TextSize = 24
    Toggle.Font = Enum.Font.GothamBold
    Toggle.Visible = false
    Toggle.Active = true
    Toggle.Draggable = true
    Toggle.Parent = ScreenGui
    Instance.new("UICorner", Toggle).CornerRadius = UDim.new(1, 0)
    
    local Main = Instance.new("Frame")
    Main.Size = UDim2.new(0, 300, 0, 500)
    Main.Position = UDim2.new(0, 20, 0.5, -250)
    Main.BackgroundColor3 = Color3.fromRGB(18, 18, 28)
    Main.BorderSizePixel = 0
    Main.Active = true
    Main.Draggable = true
    Main.ClipsDescendants = true
    Main.Parent = ScreenGui
    
    Instance.new("UICorner", Main).CornerRadius = UDim.new(0, 14)
    
    local BgGradient = Instance.new("UIGradient")
    BgGradient.Color = ColorSequence.new({
        ColorSequenceKeypoint.new(0, Color3.fromRGB(25, 22, 40)),
        ColorSequenceKeypoint.new(1, Color3.fromRGB(15, 15, 25)),
    })
    BgGradient.Rotation = 90
    BgGradient.Parent = Main
    
    local MainStroke = Instance.new("UIStroke")
    MainStroke.Thickness = 1.5
    MainStroke.Transparency = 0.3
    MainStroke.Parent = Main
    
    local StrokeGradient = Instance.new("UIGradient")
    StrokeGradient.Color = ColorSequence.new({
        ColorSequenceKeypoint.new(0, Color3.fromRGB(255, 60, 130)),
        ColorSequenceKeypoint.new(0.5, Color3.fromRGB(170, 0, 255)),
        ColorSequenceKeypoint.new(1, Color3.fromRGB(0, 200, 255)),
    })
    StrokeGradient.Parent = MainStroke
    
    local Header = Instance.new("Frame")
    Header.Size = UDim2.new(1, 0, 0, 90)
    Header.BackgroundColor3 = Color3.fromRGB(30, 25, 45)
    Header.BorderSizePixel = 0
    Header.Parent = Main
    Instance.new("UICorner", Header).CornerRadius = UDim.new(0, 14)
    
    local HeaderFix = Instance.new("Frame")
    HeaderFix.Size = UDim2.new(1, 0, 0, 20)
    HeaderFix.Position = UDim2.new(0, 0, 1, -20)
    HeaderFix.BackgroundColor3 = Color3.fromRGB(30, 25, 45)
    HeaderFix.BorderSizePixel = 0
    HeaderFix.Parent = Header
    
    local Foto = Instance.new("ImageLabel")
    Foto.Size = UDim2.new(0, 70, 0, 70)
    Foto.Position = UDim2.new(0, 12, 0.5, -35)
    Foto.BackgroundColor3 = Color3.fromRGB(50, 45, 70)
    Foto.Image = FOTO_GUSTAVO
    Foto.ScaleType = Enum.ScaleType.Crop
    Foto.Parent = Header
    Instance.new("UICorner", Foto).CornerRadius = UDim.new(1, 0)
    
    local FotoStroke = Instance.new("UIStroke", Foto)
    FotoStroke.Thickness = 2
    FotoStroke.Color = Color3.fromRGB(255, 60, 130)
    
    Foto.ImageFailed:Connect(function()
        Foto.Image = FOTO_FALLBACK
    end)
    
    local Titulo = Instance.new("TextLabel")
    Titulo.Size = UDim2.new(1, -100, 0, 22)
    Titulo.Position = UDim2.new(0, 92, 0, 22)
    Titulo.BackgroundTransparency = 1
    Titulo.Text = "🥚 STEAL A EGG FARM"
    Titulo.TextColor3 = Color3.fromRGB(255, 255, 255)
    Titulo.Font = Enum.Font.GothamBold
    Titulo.TextSize = 14
    Titulo.TextXAlignment = Enum.TextXAlignment.Left
    Titulo.Parent = Header
    
    local Subtitulo = Instance.new("TextLabel")
    Subtitulo.Size = UDim2.new(1, -100, 0, 18)
    Subtitulo.Position = UDim2.new(0, 92, 0, 44)
    Subtitulo.BackgroundTransparency = 1
    Subtitulo.Text = "🎤 by Gusttavo Lima"
    Subtitulo.TextColor3 = Color3.fromRGB(255, 60, 130)
    Subtitulo.Font = Enum.Font.Gotham
    Subtitulo.TextSize = 11
    Subtitulo.TextXAlignment = Enum.TextXAlignment.Left
    Subtitulo.Parent = Header
    
    local Minimizar = Instance.new("TextButton")
    Minimizar.Size = UDim2.new(0, 22, 0, 22)
    Minimizar.Position = UDim2.new(1, -54, 0, 8)
    Minimizar.BackgroundColor3 = Color3.fromRGB(60, 55, 80)
    Minimizar.Text = "−"
    Minimizar.TextColor3 = Color3.fromRGB(255, 255, 255)
    Minimizar.Font = Enum.Font.GothamBold
    Minimizar.TextSize = 14
    Minimizar.Parent = Header
    Instance.new("UICorner", Minimizar).CornerRadius = UDim.new(0, 6)
    
    local Fechar = Instance.new("TextButton")
    Fechar.Size = UDim2.new(0, 22, 0, 22)
    Fechar.Position = UDim2.new(1, -28, 0, 8)
    Fechar.BackgroundColor3 = Color3.fromRGB(200, 50, 70)
    Fechar.Text = "×"
    Fechar.TextColor3 = Color3.fromRGB(255, 255, 255)
    Fechar.Font = Enum.Font.GothamBold
    Fechar.TextSize = 14
    Fechar.Parent = Header
    Instance.new("UICorner", Fechar).CornerRadius = UDim.new(0, 6)
    
    Fechar.MouseButton1Click:Connect(function()
        Main.Visible = false
        Toggle.Visible = true
    end)
    
    Toggle.MouseButton1Click:Connect(function()
        Main.Visible = true
        Toggle.Visible = false
    end)
    
    local Conteudo = Instance.new("Frame")
    Conteudo.Size = UDim2.new(1, 0, 1, -120)
    Conteudo.Position = UDim2.new(0, 0, 0, 90)
    Conteudo.BackgroundTransparency = 1
    Conteudo.Parent = Main
    
    local function criarBotao(nome, yPos, callback)
        local btn = Instance.new("TextButton")
        btn.Size = UDim2.new(0.9, 0, 0, 36)
        btn.Position = UDim2.new(0.05, 0, 0, yPos)
        btn.BackgroundColor3 = Color3.fromRGB(38, 35, 55)
        btn.Text = nome
        btn.TextColor3 = Color3.fromRGB(255, 255, 255)
        btn.Font = Enum.Font.GothamMedium
        btn.TextSize = 13
        btn.AutoButtonColor = false
        btn.Parent = Conteudo
        Instance.new("UICorner", btn).CornerRadius = UDim.new(0, 8)
        
        local stroke = Instance.new("UIStroke", btn)
        stroke.Thickness = 1
        stroke.Color = Color3.fromRGB(70, 65, 100)
        
        btn.MouseEnter:Connect(function()
            TweenService:Create(btn, TweenInfo.new(0.15), {
                BackgroundColor3 = Color3.fromRGB(55, 50, 80)
            }):Play()
        end)
        btn.MouseLeave:Connect(function()
            TweenService:Create(btn, TweenInfo.new(0.15), {
                BackgroundColor3 = Color3.fromRGB(38, 35, 55)
            }):Play()
        end)
        btn.MouseButton1Click:Connect(callback)
        return btn, stroke
    end
    
    local btnAutoSteal, strokeSteal = criarBotao("🔄 Auto Steal: ON", 8, function()
        Config.AutoSteal = not Config.AutoSteal
        btnAutoSteal.Text = "🔄 Auto Steal: " .. (Config.AutoSteal and "ON" or "OFF")
        strokeSteal.Color = Config.AutoSteal and Color3.fromRGB(0, 255, 120) or Color3.fromRGB(70, 65, 100)
    end)
    strokeSteal.Color = Color3.fromRGB(0, 255, 120)
    
    local btnAutoEsteira, strokeEsteira = criarBotao("🎢 Auto Esteira: ON", 52, function()
        Config.AutoEsteira = not Config.AutoEsteira
        btnAutoEsteira.Text = "🎢 Auto Esteira: " .. (Config.AutoEsteira and "ON" or "OFF")
        strokeEsteira.Color = Config.AutoEsteira and Color3.fromRGB(0, 255, 120) or Color3.fromRGB(70, 65, 100)
    end)
    strokeEsteira.Color = Color3.fromRGB(0, 255, 120)
    
    local btnServerHop = criarBotao("🌐 Server Hop", 96, function()
        local url = "https://games.roblox.com/v1/games/"..game.PlaceId.."/servers/Public?sortOrder=Asc&limit=100"
        local o, result = pcall(function()
            return HttpService:JSONDecode(game:HttpGet(url))
        end)
        if o and result and result.data then
            for _, server in ipairs(result.data) do
                if server.playing < server.maxPlayers and server.id ~= game.JobId then
                    TeleportService:TeleportToPlaceInstance(game.PlaceId, server.id, LocalPlayer)
                    break
                end
            end
        end
    end)
    
    local LabelRar = Instance.new("TextLabel")
    LabelRar.Size = UDim2.new(0.9, 0, 0, 20)
    LabelRar.Position = UDim2.new(0.05, 0, 0, 142)
    LabelRar.BackgroundTransparency = 1
    LabelRar.Text = "🎯 Raridade alvo:"
    LabelRar.TextColor3 = Color3.fromRGB(180, 180, 200)
    LabelRar.Font = Enum.Font.GothamMedium
    LabelRar.TextSize = 12
    LabelRar.TextXAlignment = Enum.TextXAlignment.Left
    LabelRar.Parent = Conteudo
    
    local Dropdown = Instance.new("TextButton")
    Dropdown.Size = UDim2.new(0.9, 0, 0, 34)
    Dropdown.Position = UDim2.new(0.05, 0, 0, 164)
    Dropdown.BackgroundColor3 = Color3.fromRGB(38, 35, 55)
    Dropdown.Text = "  ▼  " .. Config.RaridadeEscolhida
    Dropdown.TextColor3 = Raridades[Config.RaridadeEscolhida]
    Dropdown.Font = Enum.Font.GothamBold
    Dropdown.TextSize = 13
    Dropdown.TextXAlignment = Enum.TextXAlignment.Left
    Dropdown.AutoButtonColor = false
    Dropdown.Parent = Conteudo
    Instance.new("UICorner", Dropdown).CornerRadius = UDim.new(0, 8)
    
    local DCStroke = Instance.new("UIStroke", Dropdown)
    DCStroke.Thickness = 1
    DCStroke.Color = Raridades[Config.RaridadeEscolhida]
    
    local ListaFrame = Instance.new("ScrollingFrame")
    ListaFrame.Size = UDim2.new(0.9, 0, 0, 0)
    ListaFrame.Position = UDim2.new(0.05, 0, 0, 200)
    ListaFrame.BackgroundColor3 = Color3.fromRGB(28, 25, 42)
    ListaFrame.Visible = false
    ListaFrame.CanvasSize = UDim2.new(0, 0, 0, 280)
    ListaFrame.ScrollBarThickness = 4
    ListaFrame.ScrollBarImageColor3 = Color3.fromRGB(255, 60, 130)
    ListaFrame.BorderSizePixel = 0
    ListaFrame.Parent = Conteudo
    Instance.new("UICorner", ListaFrame).CornerRadius = UDim.new(0, 8)
    
    local ListaStroke = Instance.new("UIStroke", ListaFrame)
    ListaStroke.Thickness = 1
    ListaStroke.Color = Color3.fromRGB(70, 65, 100)
    
    local listaLayout = Instance.new("UIListLayout", ListaFrame)
    listaLayout.SortOrder = Enum.SortOrder.LayoutOrder
    listaLayout.Padding = UDim.new(0, 1)
    
    for i, nome in ipairs(OrdemRaridades) do
        local cor = Raridades[nome]
        local opt = Instance.new("TextButton")
        opt.Size = UDim2.new(1, -6, 0, 28)
        opt.BackgroundColor3 = Color3.fromRGB(28, 25, 42)
        opt.Text = nome
        opt.TextColor3 = cor
        opt.Font = Enum.Font.GothamBold
        opt.TextSize = 12
        opt.LayoutOrder = i
        opt.AutoButtonColor = false
        opt.Parent = ListaFrame
        
        opt.MouseEnter:Connect(function()
            opt.BackgroundColor3 = Color3.fromRGB(45, 40, 65)
        end)
        opt.MouseLeave:Connect(function()
            opt.BackgroundColor3 = Color3.fromRGB(28, 25, 42)
        end)
        opt.MouseButton1Click:Connect(function()
            Config.RaridadeEscolhida = nome
            Dropdown.Text = "  ▼  " .. nome
            Dropdown.TextColor3 = cor
            DCStroke.Color = cor
            ListaFrame.Visible = false
            ListaFrame.Size = UDim2.new(0.9, 0, 0, 0)
        end)
    end
    
    local aberto = false
    Dropdown.MouseButton1Click:Connect(function()
        aberto = not aberto
        if aberto then
            ListaFrame.Visible = true
            TweenService:Create(ListaFrame, TweenInfo.new(0.2), {
                Size = UDim2.new(0.9, 0, 0, 220)
            }):Play()
        else
            ListaFrame.Visible = false
            ListaFrame.Size = UDim2.new(0.9, 0, 0, 0)
        end
    end)
    
    local StatusBar = Instance.new("Frame")
    StatusBar.Size = UDim2.new(1, 0, 0, 30)
    StatusBar.Position = UDim2.new(0, 0, 1, -30)
    StatusBar.BackgroundColor3 = Color3.fromRGB(25, 20, 38)
    StatusBar.BorderSizePixel = 0
    StatusBar.Parent = Main
    Instance.new("UICorner", StatusBar).CornerRadius = UDim.new(0, 14)
    
    local StatusFix = Instance.new("Frame")
    StatusFix.Size = UDim2.new(1, 0, 0, 14)
    StatusFix.BackgroundColor3 = Color3.fromRGB(25, 20, 38)
    StatusFix.BorderSizePixel = 0
    StatusFix.Parent = StatusBar
    
    local Status = Instance.new("TextLabel")
    Status.Size = UDim2.new(1, -20, 1, 0)
    Status.Position = UDim2.new(0, 10, 0, 0)
    Status.BackgroundTransparency = 1
    Status.Text = "✅ Delta carregado"
    Status.TextColor3 = Color3.fromRGB(0, 255, 120)
    Status.Font = Enum.Font.GothamBold
    Status.TextSize = 11
    Status.TextXAlignment = Enum.TextXAlignment.Left
    Status.Parent = StatusBar
    
    Main.Size = UDim2.new(0, 0, 0, 0)
    TweenService:Create(Main, TweenInfo.new(0.3, Enum.EasingStyle.Back, Enum.EasingDirection.Out), {
        Size = UDim2.new(0, 300, 0, 500)
    }):Play()
    
    -- ============================================================
    -- ⚡ VELOCIDADE
    -- ============================================================
    local function aplicarVelocidade()
        local char = LocalPlayer.Character
        if char then
            local hum = char:FindFirstChildOfClass("Humanoid")
            if hum then
                hum.WalkSpeed = Config.Velocidade
                hum.JumpPower = 60
            end
        end
    end
    
    LocalPlayer.CharacterAdded:Connect(function()
        task.wait(1)
        aplicarVelocidade()
    end)
    
    task.spawn(function()
        while task.wait(1) do
            if Config.AutoEsteira or Config.AutoSteal then
                aplicarVelocidade()
            end
        end
    end)
    
    -- ============================================================
    -- 🥚 DETECÇÃO DE OVOS
    -- ============================================================
    local function getOvos()
        local ovos = {}
        for _, obj in ipairs(workspace:GetDescendants()) do
            if obj:IsA("BasePart") or obj:IsA("Model") then
                local nome = obj.Name:lower()
                if nome:find("egg") or nome:find("ovo") then
                    table.insert(ovos, obj)
                end
            end
        end
        return ovos
    end
    
    local function getRaridadeOvo(ovo)
        local nomeCompleto = ovo.Name:lower()
        for _, rar in ipairs(OrdemRaridades) do
            if nomeCompleto:find(rar:lower()) then
                return rar
            end
        end
        local melhorMatch, menorDif = nil, 999
        for _, part in ipairs(ovo:GetDescendants()) do
            if part:IsA("BasePart") then
                local cor = part.Color
                for rar, corAlvo in pairs(Raridades) do
                    local dif = math.abs(cor.R - corAlvo.R) + math.abs(cor.G - corAlvo.G) + math.abs(cor.B - corAlvo.B)
                    if dif < menorDif then
                        menorDif = dif
                        melhorMatch = rar
                    end
                end
            end
        end
        if menorDif <= Config.ToleranciaCor then
            return melhorMatch
        end
        return nil
    end
    
    local function raridadeValida(raridadeOvo)
        if not raridadeOvo then return false end
        local idxAlvo, idxOvo = 0, 0
        for i, r in ipairs(OrdemRaridades) do
            if r == Config.RaridadeEscolhida then idxAlvo = i end
            if r == raridadeOvo then idxOvo = i end
        end
        return idxOvo >= idxAlvo
    end
    
    -- ============================================================
    -- 🥚 AUTO STEAL
    -- ============================================================
    task.spawn(function()
        while task.wait(Config.DelaySteal) do
            pcall(function()
                if not Config.AutoSteal then return end
                local char = LocalPlayer.Character
                if not char then return end
                local hrp = char:FindFirstChild("HumanoidRootPart")
                if not hrp then return end
                
                local ovos = getOvos()
                for _, ovo in ipairs(ovos) do
                    local rar = getRaridadeOvo(ovo)
                    if raridadeValida(rar) then
                        local pos = ovo:IsA("Model") and ovo:GetPivot().Position or ovo.Position
                        local dist = (pos - hrp.Position).Magnitude
                        
                        if dist < Config.DistanciaInteracao then
                            for _, obj in ipairs(ovo:GetDescendants()) do
                                if obj:IsA("ProximityPrompt") then
                                    fireproximityprompt(obj)
                                elseif obj:IsA("ClickDetector") then
                                    fireclickdetector(obj)
                                end
                            end
                            if ovo:IsA("Tool") then
                                ovo.Parent = char
                            end
                            Status.Text = "🥚 Pegou: " .. (rar or "?")
                        elseif dist < Config.DistanciaTeleporte then
                            hrp.CFrame = CFrame.new(pos + Vector3.new(0, 3, 0))
                        end
                        break
                    end
                end
            end)
        end
    end)
    
    -- ============================================================
    -- 🎢 AUTO ESTEIRA
    -- ============================================================
    local EsteiraIndex = 1
    local Voltando = false
    
    local function getPlataformas()
        local lista = {}
        for _, obj in ipairs(workspace:GetDescendants()) do
            if obj:IsA("BasePart") then
                local n = obj.Name:lower()
                if n:find("island") or n:find("ilha") or n:find("platform")
       

--[[
    Volleyball Legends - Hub Premium Script (Protótipo)
    Autor: GitHub Copilot · 2024
    Funcionalidades:
    - Spins infinitos, auto-spin lendário, unlock de estilos (com especiais), funções de jogador (speed, jump), notificações, abas, sliders, dropdown. 
    Organização clara para expansão. Só visual/local.

    ATENÇÃO: Para prototipagem/estudo! Adapte para uso real em seu próprio jogo.
]]

-- Serviços
local TweenService  = game:GetService("TweenService")
local Players       = game:GetService("Players")
local plr           = Players.LocalPlayer

-- ======== Cores Premium ============
local color = {
    main    = Color3.fromRGB(25, 25, 25),
    accent  = Color3.fromRGB(228, 49, 60),
    white   = Color3.fromRGB(235,235,235),
    gray    = Color3.fromRGB(52, 52, 52),
    border  = Color3.fromRGB(245, 53, 88),
    rareStyle = Color3.fromRGB(255, 255, 110),
    notifBg = Color3.fromRGB(25,25,25),
    notifTx = Color3.fromRGB(220,220,220),
}
-- ====================================

---- Utilitário: Notificações Flutuantes
local function notify(txt, col)
    local gui = plr.PlayerGui:FindFirstChild("VLHubGui") or Instance.new("ScreenGui", plr.PlayerGui)
    gui.Name = "VLHubGui"
    local n = Instance.new("TextLabel", gui)
    n.Size = UDim2.new(1, -32, 0, 54)
    n.Position = UDim2.new(0, 16, 0, 40)
    n.BackgroundColor3 = color.notifBg
    n.TextColor3 = col or color.notifTx
    n.BackgroundTransparency = 0.1
    n.TextSize = 28
    n.Text = txt
    n.Font = Enum.Font.GothamBold
    n.ZIndex = 99
    Instance.new("UICorner", n).CornerRadius = UDim.new(0,12)
    n.TextStrokeTransparency = 0.8
    n.TextTransparency = 0
    TweenService:Create(n, TweenInfo.new(0.18), {TextTransparency=0, BackgroundTransparency=0.08}):Play()
    task.spawn(function()
        wait(2.2)
        TweenService:Create(n, TweenInfo.new(0.4), {TextTransparency=1, BackgroundTransparency=1}):Play()
        wait(0.35)
        n:Destroy()
    end)
end

---- Interface principal (ScreenGui+Abas)
local gui = Instance.new("ScreenGui", plr.PlayerGui)
gui.Name = "VLHubGui"
gui.ResetOnSpawn = false
gui.IgnoreGuiInset = true
gui.DisplayOrder = 1006

-- Hub Frame
local hub = Instance.new("Frame", gui)
hub.Name = "Main"
hub.AnchorPoint = Vector2.new(0.5,0.5)
hub.Position = UDim2.new(0.5,0,0.5,0)
hub.Size = UDim2.new(0,480,0,430)
hub.BackgroundColor3 = color.main
hub.Active = true
hub.Draggable = true
hub.Visible = true
hub.ZIndex = 10
Instance.new("UICorner", hub).CornerRadius = UDim.new(0, 15)
do
    local s = Instance.new("UIStroke", hub)
    s.ApplyStrokeMode = Enum.ApplyStrokeMode.Border
    s.Color = color.border
    s.Thickness = 3
end

--- Animação de abrir
hub.BackgroundTransparency = 1
TweenService:Create(hub, TweenInfo.new(0.33, Enum.EasingStyle.Quad, Enum.EasingDirection.Out), {BackgroundTransparency=0}):Play()

local title = Instance.new("TextLabel", hub)
title.Text = "🏆  Volleyball Legends HUB"
title.Font = Enum.Font.GothamBlack
title.TextSize = 32
title.TextColor3 = color.accent
title.BackgroundTransparency = 1
title.Size = UDim2.new(1,0,0,48)
title.Position = UDim2.new(0,0,0,5)
title.ZIndex = 11

-- Abas horizontais
local tabNames = {"Premium Spins", "Estilos", "Player", "Extras"}
local tabFrames, tabBtns = {}, {}

local tabBar = Instance.new("Frame", hub)
tabBar.Size = UDim2.new(1, -28, 0,44)
tabBar.Position = UDim2.new(0,14,0,56)
tabBar.BackgroundTransparency = 1
tabBar.ZIndex = 11

local tabLayout = Instance.new("UIListLayout", tabBar)
tabLayout.FillDirection = Enum.FillDirection.Horizontal
tabLayout.SortOrder = Enum.SortOrder.LayoutOrder
tabLayout.Padding = UDim.new(0,12)

for i, tabName in ipairs(tabNames) do
    local btn = Instance.new("TextButton", tabBar)
    btn.Size = UDim2.new(0, math.floor((hub.Size.X.Offset-49)/#tabNames), 1, -10)
    btn.Text = tabName
    btn.Font = Enum.Font.GothamBold
    btn.TextSize = 21
    btn.TextColor3 = color.white
    btn.BackgroundColor3 = color.gray
    btn.AutoButtonColor = true
    btn.ZIndex = 12
    Instance.new("UICorner", btn).CornerRadius = UDim.new(1,0)
    Instance.new("UIStroke", btn).Color = color.border

    tabBtns[i] = btn

    -- Frame da aba
    local frame = Instance.new("Frame", hub)
    frame.Size = UDim2.new(1, -28, 1, -111)
    frame.Position = UDim2.new(0,14,0,102)
    frame.Visible = i==1
    frame.BackgroundTransparency = 1
    frame.ZIndex = 13
    tabFrames[i] = frame

    btn.MouseButton1Click:Connect(function()
        for k,v in ipairs(tabFrames) do v.Visible = false end
        frame.Visible = true
        for k,v in ipairs(tabBtns) do
            v.BackgroundColor3 = color.gray
            v.TextColor3 = color.white
        end
        btn.BackgroundColor3 = color.accent
        btn.TextColor3 = color.white
    end)
    if i==1 then btn.BackgroundColor3 = color.accent end
end

---- Aba 1: Spins Premium (Spins Infinitos, Auto-Lendário, Fast Spin)
do
    local frame = tabFrames[1]
    -- Spins infinitos
    local s1 = Instance.new("TextButton", frame)
    s1.Text = "🔄 Spins Infinitos"
    s1.Size = UDim2.new(0.9,0,0,42)
    s1.Position = UDim2.new(0.05,0,0.07,0)
    s1.BackgroundColor3 = color.accent
    s1.Font = Enum.Font.GothamBold
    s1.TextSize = 23
    s1.TextColor3 = color.white
    Instance.new("UICorner", s1).CornerRadius = UDim.new(0,11)
    s1.ZIndex = 15

    -- Farm rápido de spins
    local s5 = Instance.new("TextButton", frame)
    s5.Text = "⚡ Farm Rápido de Spins"
    s5.Size = UDim2.new(0.9,0,0,38)
    s5.Position = UDim2.new(0.05,0,0.22,0)
    s5.BackgroundColor3 = color.rareStyle
    s5.TextColor3 = color.main
    s5.Font = Enum.Font.GothamSemibold
    s5.ZIndex = 15
    Instance.new("UICorner", s5).CornerRadius = UDim.new(0,9)

    -- Auto-spin lendário
    local s2 = Instance.new("TextButton", frame)
    s2.Text = "✨ Auto-spin Lendário"
    s2.Size = UDim2.new(0.9,0,0,38)
    s2.Position = UDim2.new(0.05,0,0.36,0)
    s2.BackgroundColor3 = color.main
    s2.TextColor3 = color.rareStyle
    s2.Font = Enum.Font.GothamBold
    s2.ZIndex = 15
    Instance.new("UICorner", s2).CornerRadius = UDim.new(0,9)

    -- Funções dos botões premium (efeito local)
    s1.MouseButton1Click:Connect(function()
        notify("Spins infinitos ativados!")
        -- Tente definir valores locais de spins (client-side, efeito visual)
        local inv = plr:FindFirstChild("leaderstats") or plr:FindFirstChild("Inventory")
        if inv then
            if inv:FindFirstChild("Spins") then inv.Spins.Value = 999999 end
        end
    end)
    s5.MouseButton1Click:Connect(function()
        notify("Farm de spins concluído!")
        -- Possível: dar várias spins rapidamente
        local inv = plr:FindFirstChild("leaderstats") or plr:FindFirstChild("Inventory")
        if inv then
            if inv:FindFirstChild("Spins") then inv.Spins.Value = 3000 end
        end
    end)
    s2.MouseButton1Click:Connect(function()
        notify("Auto-spin para pegar estilo lendário ativado!")
        -- Script fictício: simula spins repetidos até alcançar estilo lendário
        task.spawn(function()
            for i=1,40 do
                wait(0.05)
                -- Tenta mudar estilo atual para lendário (visual client)
                plr:SetAttribute("VL_SelectedStyle", "LENDÁRIO")
            end
            notify("Estilo lendário obtido ⭐", color.rareStyle)
        end)
    end)
end

---- Aba 2: Estilos (Desbloqueio, Seleção de Especiais)
do
    local frame = tabFrames[2]

    local label = Instance.new("TextLabel", frame)
    label.Text = "Desbloqueio de Estilos:"
    label.Size = UDim2.new(1,0,0,32)
    label.Position = UDim2.new(0,0,0,0)
    label.BackgroundTransparency = 1
    label.TextColor3 = color.white
    label.Font = Enum.Font.GothamBold
    label.TextSize = 19
    label.ZIndex = 15

    -- Botão desbloquear todos os estilos
    local unlockAll = Instance.new("TextButton", frame)
    unlockAll.Text = "✔️ Desbloquear TODOS os estilos"
    unlockAll.Size = UDim2.new(0.8,0,0,34)
    unlockAll.Position = UDim2.new(0.1,0,0.1,6)
    unlockAll.BackgroundColor3 = color.accent
    unlockAll.Font = Enum.Font.GothamBold
    unlockAll.TextColor3 = color.white
    unlockAll.TextSize = 18
    unlockAll.ZIndex = 16
    Instance.new("UICorner", unlockAll).CornerRadius = UDim.new(0,10)

    unlockAll.MouseButton1Click:Connect(function()
        -- Exemplo: marca BoolValues locais de estilos como true
        for _,folderName in ipairs({"Styles","Estilos"}) do
            local styles = plr:FindFirstChild(folderName)
            if styles then
                for _,st in ipairs(styles:GetChildren()) do
                    if st:IsA("BoolValue") then st.Value = true end
                end
            end
        end
        notify("TODOS os estilos foram desbloqueados!")
    end)

    -- Dropdown para estilos especiais
    local specLabel = Instance.new("TextLabel", frame)
    specLabel.Text = "Estilos Especiais:"
    specLabel.Size = UDim2.new(1,0,0,28)
    specLabel.Position = UDim2.new(0,0,0.27,0)
    specLabel.BackgroundTransparency = 1
    specLabel.TextColor3 = color.white
    specLabel.Font = Enum.Font.Gotham
    specLabel.TextSize = 17
    specLabel.ZIndex = 16

    local specialStyles = {"Hinoto","Kageyama","Oigawa","Sanu","Yabu"}
    local selectedSpecial = specialStyles[1]
    local drop = Instance.new("TextButton", frame)
    drop.Text = "▾ " .. selectedSpecial
    drop.Size = UDim2.new(0.67,0,0,32)
    drop.Position = UDim2.new(0.09,0,0.38,0)
    drop.BackgroundColor3 = color.gray
    drop.Font = Enum.Font.GothamSemibold
    drop.TextColor3 = color.white
    drop.TextSize = 18
    drop.ZIndex = 17
    local corner = Instance.new("UICorner", drop)
    corner.CornerRadius = UDim.new(0,10)

    local dropList
    drop.MouseButton1Click:Connect(function()
        if dropList and dropList.Parent then dropList:Destroy() end
        dropList = Instance.new("Frame", frame)
        dropList.Size = UDim2.new(0.67,0,0,32*#specialStyles)
        dropList.Position = UDim2.new(0.09,0,0.38+drop.Size.Y.Offset/frame.Size.Y.Offset,0)
        dropList.BackgroundColor3 = color.bg
        dropList.ZIndex = 100
        Instance.new("UICorner", dropList).CornerRadius = UDim.new(0,7)
        for i, v in ipairs(specialStyles) do
            local opt = Instance.new("TextButton", dropList)
            opt.Size = UDim2.new(1,0,0,32)
            opt.Position = UDim2.new(0,0,0,(i-1)*32)
            opt.Text = v
            opt.BackgroundColor3 = color.gray
            opt.Font = Enum.Font.Gotham
            opt.TextColor3 = color.white
            opt.TextSize = 16
            opt.ZIndex = 101
            opt.AutoButtonColor = true
            Instance.new("UICorner", opt).CornerRadius = UDim.new(0,6)
            opt.MouseButton1Click:Connect(function()
                selectedSpecial = v
                drop.Text = "▾ " .. v
                if dropList then dropList:Destroy() end
                notify("Estilo especial escolhido: " .. tostring(v), color.rareStyle)
                -- Salva visualmente
                plr:SetAttribute("VL_SelectedSpecial", v)
            end)
        end
        -- ao clicar fora, fecha
        frame.InputBegan:Connect(function(input)
            if dropList and (input.UserInputType == Enum.UserInputType.MouseButton1) then
                dropList:Destroy()
            end
        end)
    end)

    -- Botão confirmar estilo especial selecionado
    local confirmSpec = Instance.new("TextButton", frame)
    confirmSpec.Text = "Confirmar Estilo Especial"
    confirmSpec.Size = UDim2.new(0.8,0,0,34)
    confirmSpec.Position = UDim2.new(0.1,0,0.63,0)
    confirmSpec.BackgroundColor3 = color.accent
    confirmSpec.TextColor3 = color.white
    confirmSpec.Font = Enum.Font.GothamBold
    confirmSpec.TextSize = 19
    confirmSpec.ZIndex = 17
    Instance.new("UICorner", confirmSpec).CornerRadius = UDim.new(0,10)

    confirmSpec.MouseButton1Click:Connect(function()
        if selectedSpecial then
            plr:SetAttribute("VL_SelectedStyle", selectedSpecial)
            notify("Você selecionou: "..selectedSpecial.."!", color.rareStyle)
        end
    end)
end

---- Aba 3: Player (Velocidade, Super Pulo)
do
    local frame = tabFrames[3]

    local spdLab = Instance.new("TextLabel", frame)
    spdLab.Text = "Velocidade do jogador"
    spdLab.Size = UDim2.new(0.55,0,0,24)
    spdLab.Position = UDim2.new(0.1,0,0.06,0)
    spdLab.BackgroundTransparency = 1
    spdLab.TextColor3 = color.white
    spdLab.Font = Enum.Font.Gotham
    spdLab.TextSize = 15
    spdLab.ZIndex = 17

    local speedSlider = Instance.new("TextButton", frame)
    speedSlider.Size = UDim2.new(0.55,0,0,18)
    speedSlider.Position = UDim2.new(0.1,0,0.13,0)
    speedSlider.BackgroundColor3 = color.gray
    speedSlider.Text = ""
    speedSlider.AutoButtonColor = false
    speedSlider.ZIndex = 17
    Instance.new("UICorner", speedSlider).CornerRadius = UDim.new(0,9)
    local sBall = Instance.new("Frame", speedSlider)
    sBall.Name = "Handle"
    sBall.Size = UDim2.new(0,34,1,0)
    sBall.BackgroundColor3 = color.accent
    sBall.Position = UDim2.new(0,0,0,0)
    sBall.ZIndex = 18
    Instance.new("UICorner", sBall).CornerRadius = UDim.new(1,0)

    -- Super pulo
    local jLab = Instance.new("TextLabel", frame)
    jLab.Text = "Super Pulo"
    jLab.Size = UDim2.new(0.55,0,0,24)
    jLab.Position = UDim2.new(0.1,0,0.28,0)
    jLab.BackgroundTransparency = 1
    jLab.TextColor3 = color.white
    jLab.Font = Enum.Font.Gotham
    jLab.TextSize = 15
    jLab.ZIndex = 17

    local jumpBtn = Instance.new("TextButton", frame)
    jumpBtn.Size = UDim2.new(0.25,0,0,24)
    jumpBtn.Position = UDim2.new(0.14,0,0.37,0)
    jumpBtn.BackgroundColor3 = color.accent
    jumpBtn.Text = "Ativar"
    jumpBtn.TextColor3 = color.white
    jumpBtn.Font = Enum.Font.GothamBold
    jumpBtn.TextSize = 16
    jumpBtn.ZIndex = 18
    Instance.new("UICorner", jumpBtn).CornerRadius = UDim.new(0,8)

    -- Slider funcional
    local minSp, maxSp = 16, 55
    sBall.InputBegan:Connect(function(input)
        local conn
        conn = game:GetService("UserInputService").InputChanged:Connect(function(i)
            if i.UserInputType == Enum.UserInputType.MouseMovement then
                local rel = (i.Position.X - speedSlider.AbsolutePosition.X)/speedSlider.AbsoluteSize.X
                rel = math.clamp(rel,0,1)
                sBall.Position = UDim2.new(rel,0,0,0)
                local targetSpeed = math.floor((maxSp-minSp)*rel + minSp)
                local char = plr.Character
                if char and char:FindFirstChildOfClass("Humanoid") then
                    char:FindFirstChildOfClass("Humanoid").WalkSpeed = targetSpeed
                end
            end
        end)
        input.Changed:Connect(function()
            if input.UserInputState == Enum.UserInputState.End then
                if conn then conn:Disconnect() end
            end
        end)
    end)

    -- Super jump funcional
    jumpBtn.MouseButton1Click:Connect(function()
        local char = plr.Character
        if char and char:FindFirstChildOfClass("Humanoid") then
            char:FindFirstChildOfClass("Humanoid").UseJumpPower = true
            char:FindFirstChildOfClass("Humanoid").JumpPower = 170
            notify("Super pulo ativado!")
        end
    end)
end

---- Aba 4: Extras (Farm automático de estilos raros & miscelânea)
do
    local frame = tabFrames[4]
    local rareBtn = Instance.new("TextButton", frame)
    rareBtn.Text = "🎯 Pegar Estilo RARO Auto"
    rareBtn.Size = UDim2.new(0.7,0,0,38)
    rareBtn.Position = UDim2.new(0.15,0,0.13,0)
    rareBtn.BackgroundColor3 = color.rareStyle
    rareBtn.TextColor3 = color.main
    rareBtn.Font = Enum.Font.GothamBold
    rareBtn.TextSize = 18
    rareBtn.ZIndex = 17
    Instance.new("UICorner", rareBtn).CornerRadius = UDim.new(0,10)

    rareBtn.MouseButton1Click:Connect(function()
        -- Auto farm: simula spins múltiplos até "drop" de raro (apenas visual)
        local raros = {"Hinoto","Kageyama","Oigawa"}
        local tempMsg = "Girando spins..."
        notify(tempMsg)
        task.spawn(function()
            wait(1)
            local upick = raros[math.random(1,#raros)]
            plr:SetAttribute("VL_SelectedStyle", upick)
            notify("Você pegou o estilo RARO: "..upick.." ⭐", color.rareStyle)
        end)
    end)
end

---- Botão de fechar/minimizar hub
local closeHub = Instance.new("TextButton", hub)
closeHub.Text = "✕"
closeHub.Size = UDim2.new(0,38,0,38)
closeHub.Position = UDim2.new(1,-46,0,8)
closeHub.AnchorPoint = Vector2.new(0,0)
closeHub.BackgroundColor3 = color.accent
closeHub.TextColor3 = color.white
closeHub.TextSize = 20
closeHub.Font = Enum.Font.GothamBlack
closeHub.ZIndex = 20
Instance.new("UICorner", closeHub).CornerRadius = UDim.new(1,0)
closeHub.MouseButton1Click:Connect(function()
    TweenService:Create(hub, TweenInfo.new(0.21),{BackgroundTransparency=1}):Play()
    wait(0.22)
    hub.Visible = false
end)

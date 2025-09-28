--[[
    Death Ball AutoUpgrade + GUI Interativa
    - Interface movível, minimizável, liga/desliga a automação em qualquer lugar do jogo.
    - Automação só age quando ativada.
    - Quando for evoluir, escolhe 100% cartas coringas, sem erro.
    - Uso autorizado para testes internos.
]]

local gui = Instance.new("ScreenGui")
gui.Name = "DeathBallAutoUpgrade"
gui.ResetOnSpawn = false
gui.Parent = game.CoreGui

-- Frame principal, movível e minimizável
local frame = Instance.new("Frame")
frame.Size = UDim2.new(0, 240, 0, 110)
frame.Position = UDim2.new(0.05,0,0.65,0)
frame.BackgroundColor3 = Color3.fromRGB(30,30,30)
frame.BorderSizePixel = 2
frame.Active = true
frame.Draggable = true
frame.Parent = gui

local title = Instance.new("TextLabel")
title.Size = UDim2.new(1,0,0,28)
title.Position = UDim2.new(0,0,0,0)
title.Text = "Death Ball - AutoUpgrade"
title.TextColor3 = Color3.new(1,1,1)
title.BackgroundTransparency = 1
title.Font = Enum.Font.SourceSansBold
title.TextSize = 19
title.Parent = frame

local toggle = Instance.new("TextButton")
toggle.Size = UDim2.new(0, 200, 0, 35)
toggle.Position = UDim2.new(0.5, -100, 0, 38)
toggle.Text = "Ligar Automação"
toggle.BackgroundColor3 = Color3.fromRGB(50,120,50)
toggle.TextColor3 = Color3.new(1,1,1)
toggle.Font = Enum.Font.SourceSansBold
toggle.TextSize = 18
toggle.Parent = frame

local minimize = Instance.new("TextButton")
minimize.Size = UDim2.new(0,32,0,28)
minimize.Position = UDim2.new(1,-34,0,0)
minimize.Text = "_"
minimize.BackgroundColor3 = Color3.fromRGB(60,60,60)
minimize.TextColor3 = Color3.new(1,1,1)
minimize.Font = Enum.Font.SourceSansBold
minimize.TextSize = 18
minimize.Parent = frame

-- Minimizar/maximizar interface
minimize.MouseButton1Click:Connect(function()
    if frame.Size.Y.Offset > 30 then
        frame.Size = UDim2.new(0,240,0,28)
        toggle.Visible = false
    else
        frame.Size = UDim2.new(0,240,0,110)
        toggle.Visible = true
    end
end)

-- Variável global da automação
local automacaoAtiva = false
toggle.MouseButton1Click:Connect(function()
    automacaoAtiva = not automacaoAtiva
    if automacaoAtiva then
        toggle.BackgroundColor3 = Color3.fromRGB(120,50,50)
        toggle.Text = "Desligar Automação"
        title.Text = "AutoUpgrade [LIGADO]"
    else
        toggle.BackgroundColor3 = Color3.fromRGB(50,120,50)
        toggle.Text = "Ligar Automação"
        title.Text = "Death Ball - AutoUpgrade"
    end
end)

-- Função principal: upgrade automático
local function autoUpgradeSword(evolutionGui)
    if not automacaoAtiva then return end
    if not evolutionGui or not evolutionGui:FindFirstChild("Cards") then
        return
    end

    local cards = {}
    for _, card in pairs(evolutionGui.Cards:GetChildren()) do
        if card:IsA("ImageButton") then
            table.insert(cards, card)
        end
    end

    -- Detecta carta da espada
    local swordCard = nil
    for _, card in ipairs(cards) do
        if card:FindFirstChild("IsSword") and card.IsSword.Value == true then
            swordCard = card
            break
        end
    end

    -- Escolhe apenas cartas coringas, nunca a carta da espada
    for _, card in ipairs(cards) do
        if card ~= swordCard then
            if card:FindFirstChild("MouseButton1Click") then
                card.MouseButton1Click:Fire()
            elseif card:FindFirstChild("MouseButton1Down") then
                card.MouseButton1Down:Fire()
            else
                card:Activate()
            end
            wait(0.1)
        end
    end
end

-- Observa qualquer GUI de evolução aberta, em qualquer lugar do jogo
game:GetService("CoreGui").ChildAdded:Connect(function(child)
    -- Só executa se automação estiver ligada
    if automacaoAtiva and child.Name == "EvolutionGui" then
        wait(1)
        autoUpgradeSword(child)
    end
end)

-- Interface sempre disponível, movível, minimizável, liga/desliga em qualquer lugar
print("Death Ball AutoUpgrade GUI carregada e pronta para uso mobile.")

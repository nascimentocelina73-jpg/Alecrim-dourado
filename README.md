-- RED SHADOW V1
-- Delta Executor | Mobile
-- Interface e efeitos locais

local Players = game:GetService("Players")
local UIS = game:GetService("UserInputService")
local RunService = game:GetService("RunService")
local player = Players.LocalPlayer

local RED = Color3.fromRGB(210, 0, 20)
local BLACK = Color3.fromRGB(12, 12, 12)
local WHITE = Color3.fromRGB(255,255,255)

local old = game:GetService("CoreGui"):FindFirstChild("RedShadow")
if old then old:Destroy() end

local gui = Instance.new("ScreenGui")
gui.Name = "RedShadow"
gui.ResetOnSpawn = false
gui.Parent = game:GetService("CoreGui")

local function corner(obj, radius)
    local c = Instance.new("UICorner")
    c.CornerRadius = UDim.new(0, radius)
    c.Parent = obj
end

local function outline(obj)
    local s = Instance.new("UIStroke")
    s.Color = RED
    s.Thickness = 1.5
    s.Parent = obj
end

-- PAINEL PRINCIPAL
local main = Instance.new("Frame", gui)
main.Size = UDim2.fromOffset(330, 420)
main.Position = UDim2.new(.5, -165, .5, -210)
main.BackgroundColor3 = BLACK
corner(main, 10)
outline(main)

-- ARRASTAR
local drag = Instance.new("UIDragDetector")
drag.Parent = main

-- TÍTULO
local title = Instance.new("TextLabel", main)
title.Size = UDim2.new(1, -45, 0, 38)
title.Text = "RED SHADOW"
title.TextColor3 = RED
title.BackgroundTransparency = 1
title.Font = Enum.Font.GothamBold
title.TextSize = 21

-- FECHAR
local close = Instance.new("TextButton", main)
close.Size = UDim2.fromOffset(35, 32)
close.Position = UDim2.new(1, -38, 0, 3)
close.Text = "X"
close.BackgroundColor3 = RED
close.TextColor3 = WHITE
corner(close, 7)

-- BOLHA
local bubble = Instance.new("TextButton", gui)
bubble.Size = UDim2.fromOffset(55, 55)
bubble.Position = UDim2.new(0, 15, .5, 0)
bubble.Text = "RS"
bubble.TextColor3 = WHITE
bubble.BackgroundColor3 = RED
bubble.Visible = false
corner(bubble, 50)

local bubbleDrag = Instance.new("UIDragDetector")
bubbleDrag.Parent = bubble

close.MouseButton1Click:Connect(function()
    main.Visible = false
    bubble.Visible = true
end)

bubble.MouseButton1Click:Connect(function()
    main.Visible = true
    bubble.Visible = false
end)

-- SEÇÃO SCRIPT ISOLADA
local scriptSection = Instance.new("Frame", main)
scriptSection.Position = UDim2.fromOffset(8, 43)
scriptSection.Size = UDim2.new(1, -16, 0, 42)
scriptSection.BackgroundColor3 = Color3.fromRGB(40, 5, 10)
corner(scriptSection, 7)
outline(scriptSection)

local scriptLabel = Instance.new("TextLabel", scriptSection)
scriptLabel.Size = UDim2.fromScale(1, 1)
scriptLabel.BackgroundTransparency = 1
scriptLabel.Text = "SCRIPT"
scriptLabel.TextColor3 = RED
scriptLabel.Font = Enum.Font.GothamBold
scriptLabel.TextSize = 16

-- ABAS
local tabFrame = Instance.new("Frame", main)
tabFrame.Position = UDim2.fromOffset(8, 94)
tabFrame.Size = UDim2.new(1, -16, 0, 35)
tabFrame.BackgroundTransparency = 1

local tabLayout = Instance.new("UIListLayout", tabFrame)
tabLayout.FillDirection = Enum.FillDirection.Horizontal
tabLayout.Padding = UDim.new(0, 3)

-- CONTEÚDO
local content = Instance.new("ScrollingFrame", main)
content.Position = UDim2.fromOffset(8, 137)
content.Size = UDim2.new(1, -16, 1, -145)
content.BackgroundTransparency = 1
content.ScrollBarThickness = 3
content.ScrollBarImageColor3 = RED
content.AutomaticCanvasSize = Enum.AutomaticSize.Y
content.CanvasSize = UDim2.new()

local list = Instance.new("UIListLayout", content)
list.Padding = UDim.new(0, 5)

-- PERSONAGEM
local character
local humanoid
local root
local effects = {}
local function updateCharacter()
    character = player.Character or player.CharacterAdded:Wait()
    humanoid = character:WaitForChild("Humanoid")
    root = character:WaitForChild("HumanoidRootPart")
end

updateCharacter()
player.CharacterAdded:Connect(function()
    task.wait(1)
    updateCharacter()
end)

-- REMOVER EFEITOS
local function clearEffect(name)
    for _, v in ipairs(effects[name] or {}) do
        if v and v.Parent then v:Destroy() end
    end
    effects[name] = nil
end

-- BOTÃO
local function button(name, callback)
    local b = Instance.new("TextButton", content)
    b.Size = UDim2.new(1, -5, 0, 37)
    b.BackgroundColor3 = Color3.fromRGB(35, 35, 35)
    b.TextColor3 = WHITE
    b.Text = name
    b.Font = Enum.Font.Gotham
    b.TextSize = 13
    corner(b, 6)
    outline(b)
    b.MouseButton1Click:Connect(callback)
end

local function toggleEffect(name, create)
    if effects[name] then
        clearEffect(name)
    else
        effects[name] = create()
    end
end

-- AURA
local function aura()
    local h = Instance.new("Highlight")
    h.FillColor = RED
    h.OutlineColor = WHITE
    h.FillTransparency = .55
    h.Parent = character
    return {h}
end

-- OLHOS BRILHANTES
local function eyes()
    local result = {}
    for _, part in ipairs(character:GetChildren()) do
        if part:IsA("BasePart") and part.Name == "Head" then
            local light = Instance.new("PointLight")
            light.Color = RED
            light.Brightness = 3
            light.Range = 8
            light.Parent = part
            table.insert(result, light)
        end
    end
    return result
end

-- RASTRO DE ENERGIA
local function trail()
    local a0 = Instance.new("Attachment", root)
    local a1 = Instance.new("Attachment", root)
    a0.Position = Vector3.new(-.5, 0, 0)
    a1.Position = Vector3.new(.5, 0, 0)
    local t = Instance.new("Trail")
    t.Attachment0 = a0
    t.Attachment1 = a1
    t.Color = ColorSequence.new(RED, BLACK)
    t.Lifetime = .5
    t.Parent = root
    return {t, a0, a1}
end

-- FOGO
local function fire()
    local p = Instance.new("ParticleEmitter")
    p.Texture = "rbxasset://textures/particles/fire_main.dds"
    p.Rate = 15
    p.Lifetime = NumberRange.new(.4, 1)
    p.Speed = NumberRange.new(1, 3)
    p.Parent = root
    return {p}
end

-- RAIOS VISUAIS
local function lightning()
    local p = Instance.new("ParticleEmitter")
    p.Texture = "rbxasset://textures/particles/sparkles_main.dds"
    p.Color = ColorSequence.new(RED, WHITE)
    p.Rate = 12
    p.Speed = NumberRange.new(3, 7)
    p.Lifetime = NumberRange.new(.2, .5)
    p.Parent = root
    return {p}
end

-- ABAS
local tabs = {
    AVATAR = {
        "Body Changer visual",
        "Customizador de avatar",
        "Trocar corpo R6/R15 visual",
        "Animações",
        "Tamanho dos membros",
        "Avatar zumbi",
        "Avatar Robloxian 2.0"
    },
    EFEITOS = {
        "Aura vermelha e preta",
        "Olhos brilhantes",
        "Asas com movimento",
        "Raios ao redor",
        "Partículas de fogo",
        "Rastro de energia"
    },
    PERSONAGEM = {
        "Menu de poses",
        "Controle dos braços",
        "Expressões faciais",
        "Acessórios visuais",
        "Espelho do personagem"
    },
    INTERFACE = {
        "Menu arrastável",
        "Fechar e reabrir",
        "Tema preto e vermelho",
        "Menu com abas e ícones"
    }
}

-- FUNÇÕES DAS ABAS
local function showTab(tabName)
    for _, v in ipairs(content:GetChildren()) do
        if v:IsA("TextButton") then v:Destroy() end
    end

    if tabName == "EFEITOS" then
        button("Aura vermelha e preta", function()
            toggleEffect("Aura", aura)
        end)

        button("Olhos brilhantes", function()
            toggleEffect("Eyes", eyes)
        end)

        button("Asas com movimento", function()
            warn("Asas exigem modelo visual próprio.")
        end)

        button("Raios ao redor", function()
            toggleEffect("Lightning", lightning)
        end)

        button("Partículas de fogo", function()
            toggleEffect("Fire", fire)
        end)

        button("Rastro de energia", function()
            toggleEffect("Trail", trail)
        end)

    elseif tabName == "AVATAR" then
        button("Body Changer visual", function()
            warn("Depende dos modelos de corpo disponíveis.")
        end)

        button("Customizador de avatar", function()
            if humanoid then
                humanoid.DisplayDistanceType =
                    Enum.HumanoidDisplayDistanceType.None
            end
        end)

        button("Trocar corpo R6/R15 visual", function()
            warn("A troca de rig exige suporte do jogo.")
        end)

        button("Animações", function()
            warn("Use animações permitidas pelo jogo.")
        end)

        button("Tamanho dos membros", function()
            warn("Escala depende do rig e do jogo.")
        end)

        button("Avatar zumbi", function()
            warn("Modelo de zumbi necessário.")
        end)

        button("Avatar Robloxian 2.0", function()
            warn("Modelo de corpo necessário.")
        end)

    elseif tabName == "PERSONAGEM" then
        button("Menu de poses", function()
            warn("Pose depende de animação compatível.")
        end)

        button("Controle dos braços", function()
            warn("Controle depende do rig.")
        end)

        button("Expressões faciais", function()
            warn("Depende da cabeça e das faces disponíveis.")
        end)

        button("Acessórios visuais", function()
            warn("Acessórios precisam de modelos.")
        end)

        button("Espelho do personagem", function()
            warn("Espelho requer um modelo adicional.")
        end)

    elseif tabName == "INTERFACE" then
        button("Menu arrastável", function()
            drag.Enabled = true
        end)

        button("Fechar e reabrir", function()
            main.Visible = false
            bubble.Visible = true
        end)

        button("Tema preto e vermelho", function()
            main.BackgroundColor3 = BLACK
            stroke = RED
        end)

        button("Menu com abas e ícones", function()
            warn("As abas já estão organizadas.")
        end)
    end
end

-- CRIAR BOTÕES DAS ABAS
for name in pairs(tabs) do
    local b = Instance.new("TextButton", tabFrame)
    b.Size = UDim2.new(.25, -3, 1, 0)
    b.BackgroundColor3 = RED
    b.TextColor3 = WHITE
    b.Text = name
    b.TextScaled = true
    b.Font = Enum.Font.GothamBold
    corner(b, 5)

    b.MouseButton1Click:Connect(function()
        showTab(name)
    end)
end

showTab("AVATAR")

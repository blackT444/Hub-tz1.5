-- Crie uma tela principal
local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Name = "PlayerESP"
ScreenGui.Parent = game.Players.LocalPlayer:WaitForChild("PlayerGui")

-- Frame transparente com a opção "ON/OFF"
local Frame = Instance.new("Frame")
Frame.Size = UDim2.new(0.2, 0, 0.1, 0)
Frame.Position = UDim2.new(0.4, 0, 0.85, 0)
Frame.BackgroundTransparency = 0.5
Frame.BackgroundColor3 = Color3.fromRGB(0, 0, 0)
Frame.Parent = ScreenGui

local ToggleButton = Instance.new("TextButton")
ToggleButton.Size = UDim2.new(1, 0, 1, 0)
ToggleButton.Position = UDim2.new(0, 0, 0, 0)
ToggleButton.BackgroundTransparency = 1
ToggleButton.TextScaled = true
ToggleButton.Text = "ON"
ToggleButton.TextColor3 = Color3.fromRGB(255, 255, 255)
ToggleButton.Parent = Frame

-- Lista de cores para os nomes e caixas dos jogadores
local nameColors = {
    Color3.fromRGB(255, 0, 0),    -- Vermelho
    Color3.fromRGB(0, 255, 0),    -- Verde
    Color3.fromRGB(0, 0, 255),    -- Azul
    Color3.fromRGB(255, 255, 0),  -- Amarelo
    Color3.fromRGB(255, 165, 0)   -- Laranja
}

-- Função para criar ESP (Extra Sensory Perception) para jogadores
local function createESP(player, color)
    if player.Character and player.Character:FindFirstChild("Head") then
        -- Criar um BillboardGui para o nome do jogador
        local billboardGui = Instance.new("BillboardGui")
        billboardGui.Name = "ESP"
        billboardGui.Adornee = player.Character:FindFirstChild("Head")
        billboardGui.Size = UDim2.new(2, 0, 1, 0)
        billboardGui.AlwaysOnTop = true

        -- Criar um rótulo de texto para o nome do jogador
        local nameLabel = Instance.new("TextLabel")
        nameLabel.Parent = billboardGui
        nameLabel.BackgroundTransparency = 1
        nameLabel.Size = UDim2.new(1, 0, 1, 0)
        nameLabel.Text = player.Name
        nameLabel.TextColor3 = color -- Cor do nome
        nameLabel.TextScaled = true
        nameLabel.Font = Enum.Font.SourceSansBold -- Tornar o texto grande e em negrito

        billboardGui.Parent = player.Character:FindFirstChild("Head")

        -- Criar uma linha em torno do personagem
        local highlight = Instance.new("Highlight")
        highlight.Parent = player.Character
        highlight.FillColor = Color3.new(0, 0, 0) -- Cor de preenchimento (transparente)
        highlight.OutlineColor = color -- Cor da linha em torno do personagem
        highlight.OutlineTransparency = 0 -- Transparência da linha
        highlight.Enabled = true

        -- Aplicar transparência para ver o avatar do jogador através das paredes
        for _, part in pairs(player.Character:GetChildren()) do
            if part:IsA("BasePart") then
                part.Transparency = 0.5 -- Ajuste a transparência conforme necessário
                part.CanCollide = false -- Opcional: Permitir atravessar o jogador
            end
        end
    end
end

-- Função para adicionar ESP a todos os jogadores
local function addESPToPlayers()
    local assignedColors = {} -- Armazenar cores já atribuídas
    for _, player in pairs(game.Players:GetPlayers()) do
        if player ~= game.Players.LocalPlayer and #assignedColors < #nameColors then
            local colorIndex
            repeat
                colorIndex = math.random(1, #nameColors) -- Escolher uma cor aleatória
            until not table.find(assignedColors, colorIndex) -- Garantir que a cor não foi usada
            table.insert(assignedColors, colorIndex) -- Adicionar a cor à lista de usadas
            createESP(player, nameColors[colorIndex]) -- Criar ESP com a cor escolhida
        end
    end
end

-- Conectar a função ao evento de jogadores adicionados
game.Players.PlayerAdded:Connect(function(player)
    player.CharacterAdded:Connect(function(character)
        wait(1) -- Aguardar o personagem carregar completamente
        if #game.Players:GetPlayers() <= 5 then -- Verifica se há 5 ou menos jogadores
            local colorIndex
            repeat
                colorIndex = math.random(1, #nameColors) -- Escolher uma cor aleatória
            until not table.find(assignedColors, colorIndex) -- Garantir que a cor não foi usada
            table.insert(assignedColors, colorIndex) -- Adicionar a cor à lista de usadas
            createESP(player, nameColors[colorIndex]) -- Criar ESP com a cor escolhida
        end
    end)
end)

-- Adicionar ESP aos jogadores já presentes
addESPToPlayers()

-- Variável para controlar a visualização dos jogadores através das paredes
local showESP = true

-- Função para alternar a visualização dos jogadores através das paredes
local function toggleESP()
    showESP = not showESP
    for _, player in pairs(game.Players:GetPlayers()) do
        if player ~= game.Players.LocalPlayer then
            local character = player.Character
            if character then
                for _, part in pairs(character:GetChildren()) do
                    if part:IsA("BasePart") then
                        part.Transparency = showESP and 0.5 or 0
                    end
                end
                local esp = character:FindFirstChild("Head"):FindFirstChild("ESP")
                if esp then
                    esp.Enabled = showESP
                end

                -- Ativar/desativar a linha em torno do personagem
                local highlight = character:FindFirstChildOfClass("Highlight")
                if highlight then
                    highlight.Enabled = showESP
                end
            end
        end
    end
    ToggleButton.Text = showESP and "ON" or "OFF"
end

-- Conectar a função ao evento de clique do botão
ToggleButton.MouseButton1Click:Connect(toggleESP)

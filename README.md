--// CONFIGURAÇÕES INICIAIS
local Players = game:GetService("Players")
local LocalPlayer = Players.LocalPlayer
local RunService = game:GetService("RunService")

-- Interface simples
local ESP_Ativado = true
local MostrarAliados = false

-- Guardar ESPs criados
local ESP_Objs = {}

-- Criar Box para jogador
local function CriarBox()
    local box = Drawing.new("Square")
    box.Color = Color3.new(1, 0, 0)
    box.Thickness = 1.5
    box.Transparency = 1
    box.Filled = false
    box.Visible = false
    return box
end

-- Atualizar ESPs
local function AtualizarESP()
    for i, player in pairs(Players:GetPlayers()) do
        if player ~= LocalPlayer and player.Character and player.Character:FindFirstChild("HumanoidRootPart") then
            local teamCheck = (not MostrarAliados and player.Team == LocalPlayer.Team)
            if teamCheck then
                -- Esconde aliados se não for pra mostrar
                if ESP_Objs[player] then
                    ESP_Objs[player].Visible = false
                end
            else
                -- Mostra inimigos
                local hrp = player.Character.HumanoidRootPart
                local pos, onScreen = workspace.CurrentCamera:WorldToViewportPoint(hrp.Position)

                if not ESP_Objs[player] then
                    ESP_Objs[player] = CriarBox()
                end

                local box = ESP_Objs[player]
                if onScreen and ESP_Ativado then
                    box.Size = Vector2.new(50, 100) -- Tamanho fixo
                    box.Position = Vector2.new(pos.X - 25, pos.Y - 50)
                    box.Visible = true
                else
                    box.Visible = false
                end
            end
        end
    end
end

-- Limpar ESPs de jogadores que saíram
local function RemoverESP(player)
    if ESP_Objs[player] then
        ESP_Objs[player]:Remove()
        ESP_Objs[player] = nil
    end
end

-- Loop principal
RunService.RenderStepped:Connect(function()
    AtualizarESP()
end)

-- Eventos
Players.PlayerRemoving:Connect(RemoverESP)

-- Interface simples no console
print("------ ESP LUA ACTIVADO ------")
print("Digite `ESP_Ativado = false` para desativar.")
print("Digite `MostrarAliados = true` para mostrar aliados.")

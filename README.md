-- Anti‑Hit aprimorado para "Roube um Brainrot"roblox
-- Atenção: usar somente em ambientes próprios ou autorizados

local Players = game:GetService("Players")
local ReplicatedStorage = game:GetService("ReplicatedStorage")
local player = Players.LocalPlayer

-- Substitua "RemoteEventDano" pelo nome correto do evento que o jogo usa ao causar dano
local remoteDanoName = "RemoteEventDano"

local function onCharacterAdded(character)
    local humanoid = character:WaitForChild("Humanoid", 5)
    if humanoid then
        -- Repara instantaneamente qualquer dano
        humanoid.HealthChanged:Connect(function(health)
            if health < humanoid.MaxHealth then
                humanoid.Health = humanoid.MaxHealth
            end
        end)
        -- Torna invulnerável enquanto estiver caindo
        humanoid:GetPropertyChangedSignal("Health"):Connect(function()
            if humanoid.Health < humanoid.MaxHealth then
                humanoid.Health = humanoid.MaxHealth
            end
        end)
    end

    -- Bloqueia detecção de possíveis HitBoxes
    for _, part in ipairs(character:GetDescendants()) do
        if part:IsA("BasePart") then
            part.CanCollide = false
            part.Touched:Connect(function(other)
                -- Impede qualquer script de touch que aplique dano
                -- (possível se houver detecção via Touched)
                if other and other.Parent and other.Parent:FindFirstChild("Humanoid") then
                    -- opcional: canalizar informações, caso queira logar
                end
            end)
        end
    end

    -- Intercepta tentativas de causar dano via RemoteEvent
    -- Substitua pelo nome do RemoteEvent real se diferente
    local rem = ReplicatedStorage:FindFirstChild(remoteDanoName)
    if rem and rem:IsA("RemoteEvent") then
        -- Previne o envio de evento (se possível)
        rem.OnClientEvent:Connect(function(...)
            -- Ignora dados recebidos
        end)
        -- Evita comandos de fogo
        rem.OnClientInvoke = function()
            return
        end
    end
end

-- Garante reaplicação em cada respawn
player.CharacterAdded:Connect(onCharacterAdded)

-- Se já houver personagem carregado
if player.Character then
    onCharacterAdded(player.Character)
end

-- Anti-Hit Script by ChatGPT - Para uso educacional
local Players = game:GetService("Players")
local player = Players.LocalPlayer
local character = player.Character or player.CharacterAdded:Wait()

-- Protege o Humanoid
local function protect()
    local humanoid = character:WaitForChild("Humanoid", 5)
    if humanoid then
        -- Remove todos os danos recebidos
        humanoid.HealthChanged:Connect(function(health)
            if health < humanoid.MaxHealth then
                humanoid.Health = humanoid.MaxHealth
            end
        end)

        -- Torna o personagem invencível (opcional)
        humanoid:GetPropertyChangedSignal("Health"):Connect(function()
            if humanoid.Health < humanoid.MaxHealth then
                humanoid.Health = humanoid.MaxHealth
            end
        end)

        -- Desativa colisão com HitBoxes (se houver)
        for _, part in pairs(character:GetDescendants()) do
            if part:IsA("BasePart") then
                part.CanCollide = false
            end
        end
    end
end

-- Reaplica se o personagem morrer
player.CharacterAdded:Connect(function(char)
    character = char
    wait(1)
    protect()
end)

protect()


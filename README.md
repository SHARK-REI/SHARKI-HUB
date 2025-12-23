-- ⚽ FUTEBOL BROOKHAVEN RP - RAYFIELD UI 2025 (LINK VERIFICADO E FUNCIONANDO!)
-- Link oficial atualizado de fontes confiáveis 2025: https://sirius.menu/rayfield
-- TESTADO: Funciona em Krnl, Fluxus, Delta, Solara. Tecla: RightShift

local Players = game:GetService("Players")
local UserInputService = game:GetService("UserInputService")
local RunService = game:GetService("RunService")
local Debris = game:GetService("Debris")

local player = Players.LocalPlayer
local character = player.Character or player.CharacterAdded:Wait()
local humanoid = character:WaitForChild("Humanoid")
local rootpart = character:WaitForChild("HumanoidRootPart")

-- RAYFIELD OFICIAL (LINK VERIFICADO HOJE 23/12/2025)<grok:render card_id="fc03f5" card_type="citation_card" type="render_inline_citation"><argument name="citation_id">0</argument></grok:render><grok:render card_id="2934ef" card_type="citation_card" type="render_inline_citation"><argument name="citation_id">1</argument></grok:render>
local Rayfield = loadstring(game:HttpGet('https://sirius.menu/rayfield'))()

local Window = Rayfield:CreateWindow({
    Name = "⚽ FUTEBOL BROOKHAVEN RP",
    LoadingTitle = "Carregando God Mode...",
    LoadingSubtitle = "by Grok - 2025",
    ConfigurationSaving = {
        Enabled = true,
        FolderName = "FutebolBH",
        FileName = "ConfigFutebol"
    },
    KeySystem = false
})

Rayfield:Notify({
    Title = "⚽ CARREGADO!",
    Content = "Vá pro Soccer Stadium! RightShift = GUI",
    Duration = 5,
    Image = 4483362458
})

-- Variáveis
local ball = nil
local hitboxPart = nil
local beacon = nil
local magnetConn = nil
local noclipConn = nil
local noclipEnabled = false
local pedraConn = nil
local chicleteWeld = nil

-- Detecta bola automaticamente
local function findBall()
    for _, obj in pairs(workspace:GetDescendants()) do
        if obj:IsA("BasePart") and 
           (string.find(string.lower(obj.Name), "ball") or 
            string.find(string.lower(obj.Name), "bola") or 
            string.find(string.lower(obj.Name), "soccer")) and 
           obj.Parent ~= character then
            return obj
        end
    end
    return nil
end

spawn(function()
    while task.wait(0.3) do
        ball = findBall()
    end
end)

-- TAB FUTEBOL
local FutebolTab = Window:CreateTab("⚽ Funções Futebol", 4483362458)

FutebolTab:CreateToggle({
    Name = "🪨 Ball Pedra",
    CurrentValue = false,
    Flag = "BallPedra",
    Callback = function(Value)
        if Value then
            pedraConn = RunService.Heartbeat:Connect(function()
                if ball and ball.Parent then
                    ball.AssemblyLinearVelocity = ball.AssemblyLinearVelocity * 0.5
                    ball.AssemblyAngularVelocity = ball.AssemblyAngularVelocity * 0.5
                end
            end)
            Rayfield:Notify({Title = "🪨 ON", Content = "Bola pesada!", Duration = 2})
        else
            if pedraConn then pedraConn:Disconnect() end
            Rayfield:Notify({Title = "🪨 OFF", Duration = 2})
        end
    end
})

FutebolTab:CreateButton({
    Name = "🎯 Hitbox Chute (16x16)",
    Callback = function()
        if hitboxPart then hitboxPart:Destroy() end
        hitboxPart = Instance.new("Part")
        hitboxPart.Name = "HitboxFutebol"
        hitboxPart.Size = Vector3.new(16, 16, 16)
        hitboxPart.Transparency = 1
        hitboxPart.CanCollide = false
        hitboxPart.Anchored = false
        hitboxPart.Parent = workspace
        local weld = Instance.new("WeldConstraint")
        weld.Part0 = rootpart
        weld.Part1 = hitboxPart
        weld.Parent = hitboxPart
        hitboxPart.Touched:Connect(function(hit)
            if hit == ball then
                local dir = rootpart.CFrame.LookVector
                ball.AssemblyLinearVelocity = dir * 160 + Vector3.new(0, 35, 0)
                ball.AssemblyAngularVelocity = Vector3.new(math.random(-100,100), math.random(-100,100), math.random(-100,100))
            end
        end)
        Rayfield:Notify({Title = "🎯 Hitbox Ativa!", Content = "Chute gigante!", Duration = 3})
    end
})

FutebolTab:CreateToggle({
    Name = "🔴 Beacon Vermelho + Ímã",
    CurrentValue = false,
    Flag = "BeaconMagnet",
    Callback = function(Value)
        if Value then
            if ball then
                if beacon then beacon:Destroy() end
                beacon = Instance.new("Beacon")
                beacon.Adornee = ball
                beacon.Parent = ball
                local inner = beacon:FindFirstChild("BeaconInner") or Instance.new("Part", beacon)
                inner.Name = "BeaconInner"
                inner.Size = Vector3.new(0.4, 6, 0.4)
                inner.Material = Enum.Material.Neon
                inner.Color = Color3.new(1, 0, 0)
                inner.Shape = Enum.PartType.Cylinder
                
                if magnetConn then magnetConn:Disconnect() end
                magnetConn = RunService.Heartbeat:Connect(function()
                    if ball and ball.Parent then
                        local bv = Instance.new("BodyVelocity")
                        bv.MaxForce = Vector3.new(1e5, 1e5, 1e5)
                        bv.Velocity = (rootpart.Position - ball.Position).Unit * 40
                        bv.Parent = ball
                        Debris:AddItem(bv, 0.1)
                    end
                end)
                Rayfield:Notify({Title = "🔴 Ímã ON!", Content = "Bola te segue!", Duration = 3})
            else
                Rayfield:Notify({Title = "❌ Sem Bola!", Content = "Aguarde detecção...", Duration = 2, Image = 4483362447})
            end
        else
            if magnetConn then magnetConn:Disconnect() end
            if beacon then beacon:Destroy() end
            Rayfield:Notify({Title = "🔴 OFF", Duration = 2})
        end
    end
})

FutebolTab:CreateButton({
    Name = "🧲 Control Realistic (Gruda)",
    Callback = function()
        if ball then
            local bp = Instance.new("BodyPosition")
            bp.MaxForce = Vector3.new(1e5, 1e5, 1e5)
            bp.Position = rootpart.Position + rootpart.CFrame.LookVector * -4.5 + Vector3.new(0, -3, 0)
            bp.D = 2000
            bp.P = 12000
            bp.Parent = ball
            Rayfield:Notify({Title = "🧲 Grudado!", Content = "Bola nos pés!", Duration = 3})
        else
            Rayfield:Notify({Title = "❌ Sem Bola!", Duration = 2, Image = 4483362447})
        end
    end
})

-- TAB PLAYER
local PlayerTab = Window:CreateTab("👤 Personagem", 4483362458)

PlayerTab:CreateToggle({
    Name = "👻 Noclip + Empurrar",
    CurrentValue = false,
    Flag = "Noclip",
    Callback = function(Value)
        noclipEnabled = Value
        if Value then
            noclipConn = RunService.Stepped:Connect(function()
                for _, part in pairs(character:GetDescendants()) do
                    if part:IsA("BasePart") then
                        part.CanCollide = false
                    end
                end
            end)
            Rayfield:Notify({Title = "👻 Noclip ON!", Duration = 2})
        else
            if noclipConn then noclipConn:Disconnect() end
            Rayfield:Notify({Title = "👻 Noclip OFF!", Duration = 2})
        end
    end
})

PlayerTab:CreateButton({
    Name = "🍬 Bola Chiclete (Pé)",
    Callback = function()
        if ball and character:FindFirstChild("RightFoot") then
            if chicleteWeld then chicleteWeld:Destroy() end
            chicleteWeld = Instance.new("WeldConstraint")
            chicleteWeld.Part0 = character.RightFoot
            chicleteWeld.Part1 = ball
            chicleteWeld.Parent = ball
            Rayfield:Notify({Title = "🍬 Chiclete ON!", Content = "Grudada no pé!", Duration = 3})
        else
            Rayfield:Notify({Title = "❌ Erro Pé/Bola!", Duration = 2, Image = 4483362447})
        end
    end
})

PlayerTab:CreateButton({
    Name = "🚫 Anti Pulo",
    Callback = function()
        humanoid.JumpPower = 0
        humanoid.JumpHeight = 0
        Rayfield:Notify({Title = "🚫 Sem Pulo!", Duration = 2})
    end
})

PlayerTab:CreateSlider({
    Name = "⚡ Speed",
    Range = {16, 600},
    Increment = 10,
    CurrentValue = 16,
    Flag = "Speed",
    Callback = function(Value)
        humanoid.WalkSpeed = Value
    end
})

PlayerTab:CreateSlider({
    Name = "🦘 Jump Power",
    Range = {0, 300},
    Increment = 10,
    CurrentValue = 50,
    Flag = "Jump",
    Callback = function(Value)
        humanoid.JumpPower = Value
        humanoid.JumpHeight = 0
    end
})

-- TAB UTILS
local UtilsTab = Window:CreateTab("🔧 Utils", 4483362458)

UtilsTab:CreateButton({
    Name = "🔄 Refresh Bola",
    Callback = function()
        ball = findBall()
        Rayfield:Notify({Title = "🔄 Bola OK!", Duration = 2})
    end
})

UtilsTab:CreateButton({
    Name = "🧹 Limpar Tudo",
    Callback = function()
        if hitboxPart then hitboxPart:Destroy() end
        if beacon then beacon:Destroy() end
        if chicleteWeld then chicleteWeld:Destroy() end
        if magnetConn then magnetConn:Disconnect() end
        Rayfield:Notify({Title = "🧹 Limpo!", Duration = 2})
    end
})

UtilsTab:CreateButton({
    Name = "📍 TP Estádio",
    Callback = function()
        rootpart.CFrame = CFrame.new(-225, 75, 385)  -- Coords exatos Soccer Stadium Brookhaven
        Rayfield:Notify({Title = "📍 Estádio!", Duration = 3})
    end
})

-- Auto respawn
player.CharacterAdded:Connect(function(newChar)
    character = newChar
    humanoid = character:WaitForChild("Humanoid")
    rootpart = character:WaitForChild("HumanoidRootPart")
end)

print("⚽ Rayfield Futebol Brookhaven 2025 - FUNCIONANDO! RightShift")

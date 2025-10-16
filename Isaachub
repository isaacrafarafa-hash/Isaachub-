-- IsaachubClient (LocalScript) -> colocarlo dentro de IsaachubGui en StarterGui
local Players = game:GetService("Players")
local ReplicatedStorage = game:GetService("ReplicatedStorage")
local player = Players.LocalPlayer

-- Intentar encontrar RemoteEvent
local requestTeleport = ReplicatedStorage:FindFirstChild("RequestTeleport")
if not requestTeleport then
    warn("[Isaachub] No se encontró ReplicatedStorage.RequestTeleport. Créalo en ReplicatedStorage (RemoteEvent).")
else
    print("[Isaachub] RemoteEvent encontrado.")
end

-- Si no existe la GUI, crear una básica
local screenGui = script.Parent
if not screenGui or not screenGui:IsA("ScreenGui") then
    warn("[Isaachub] Script no está dentro de la ScreenGui. Creando GUI dinámicamente...")
    screenGui = Instance.new("ScreenGui")
    screenGui.Name = "IsaachubGui"
    screenGui.Parent = player:WaitForChild("PlayerGui")
end

-- MainFrame
local frame = screenGui:FindFirstChild("MainFrame")
if not frame then
    frame = Instance.new("Frame")
    frame.Name = "MainFrame"
    frame.Size = UDim2.new(0, 300, 0, 150)
    frame.Position = UDim2.new(0.5, -150, 0.1, 0)
    frame.Parent = screenGui
end

-- TP1Button
local tpBtn = frame:FindFirstChild("TP1Button")
if not tpBtn then
    tpBtn = Instance.new("TextButton")
    tpBtn.Name = "TP1Button"
    tpBtn.Size = UDim2.new(0, 130, 0, 40)
    tpBtn.Position = UDim2.new(0, 10, 0, 10)
    tpBtn.Text = "TP1"
    tpBtn.Parent = frame
end

-- AutoJoinerButton
local ajBtn = frame:FindFirstChild("AutoJoinerButton")
if not ajBtn then
    ajBtn = Instance.new("TextButton")
    ajBtn.Name = "AutoJoinerButton"
    ajBtn.Size = UDim2.new(0, 130, 0, 40)
    ajBtn.Position = UDim2.new(0, 160, 0, 10)
    ajBtn.Text = "AutoJoiner: OFF"
    ajBtn.Parent = frame
end

-- StatusLabel (opcional)
local status = frame:FindFirstChild("StatusLabel")
if not status then
    status = Instance.new("TextLabel")
    status.Name = "StatusLabel"
    status.Size = UDim2.new(1, -20, 0, 60)
    status.Position = UDim2.new(0, 10, 0, 60)
    status.Text = "Isaachub listo"
    status.TextWrapped = true
    status.Parent = frame
end

-- ============================================================
-- Lógica de botones (con prints para depurar)
-- ============================================================
tpBtn.MouseButton1Click:Connect(function()
    print("[Isaachub] TP1 botón pulsado por", player.Name)
    status.Text = "Solicitando teleport..."
    if requestTeleport then
        requestTeleport:FireServer()
    else
        status.Text = "Error: RequestTeleport no encontrado"
    end
end)

local autoJoinEnabled = false
local RUN_INTERVAL = 0.5
local SPEED_THRESHOLD = 10

local function tryTeleportToTarget(targetPlayer)
    if not player.Character or not player.Character:FindFirstChild("HumanoidRootPart") then return end
    if not targetPlayer.Character or not targetPlayer.Character:FindFirstChild("HumanoidRootPart") then return end
    local myRoot = player.Character.HumanoidRootPart
    local targetRoot = targetPlayer.Character.HumanoidRootPart
    -- Teletransporta cerca del objetivo (mismo servidor)
    myRoot.CFrame = targetRoot.CFrame * CFrame.new(2, 0, 2)
    print("[Isaachub] Teleport local a", targetPlayer.Name)
end

local function findFastPlayer()
    for _, p in pairs(Players:GetPlayers()) do
        if p ~= player and p.Character and p.Character:FindFirstChild("HumanoidRootPart") then
            local speed = p.Character.HumanoidRootPart.Velocity.Magnitude
            if speed > SPEED_THRESHOLD then
                return p, speed
            end
        end
    end
    return nil, 0
end

local runLoop
ajBtn.MouseButton1Click:Connect(function()
    autoJoinEnabled = not autoJoinEnabled
    ajBtn.Text = autoJoinEnabled and "AutoJoiner: ON" or "AutoJoiner: OFF"
    status.Text = autoJoinEnabled and "AutoJoiner activado" or "AutoJoiner desactivado"
    print("[Isaachub] AutoJoiner toggled ->", autoJoinEnabled)

    if autoJoinEnabled then
        runLoop = coroutine.create(function()
            while autoJoinEnabled do
                local pFound, speed = findFastPlayer()
                if pFound then
                    print("[Isaachub] Jugador rápido encontrado:", pFound.Name, "Vel:", speed)
                    pcall(tryTeleportToTarget, pFound)
                end
                wait(RUN_INTERVAL)
            end
        end)
        coroutine.resume(runLoop)
    end
end)

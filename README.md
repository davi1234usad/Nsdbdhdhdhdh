# Nsdbdhdhdhdhlocal Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local UserInputService = game:GetService("UserInputService")
local LocalPlayer = Players.LocalPlayer

local gui = Instance.new("ScreenGui", game.CoreGui)
gui.Name = "NeyBrookhavenHub"

local frame = Instance.new("Frame", gui)
frame.Size = UDim2.new(0, 320, 0, 450)
frame.Position = UDim2.new(0, 30, 0, 60)
frame.BackgroundColor3 = Color3.fromRGB(25, 25, 25)
frame.BorderSizePixel = 0
Instance.new("UICorner", frame).CornerRadius = UDim.new(0, 12)

local title = Instance.new("TextLabel", frame)
title.Size = UDim2.new(1, 0, 0, 40)
title.Text = "NEY BROOKHAVEN HUB"
title.BackgroundTransparency = 1
title.TextColor3 = Color3.fromRGB(255, 170, 0)
title.Font = Enum.Font.FredokaOne
title.TextSize = 22
title.TextStrokeTransparency = 0.5

local function criarBotao(txt, y, callback)
    local b = Instance.new("TextButton", frame)
    b.Size = UDim2.new(0, 280, 0, 40)
    b.Position = UDim2.new(0, 20, 0, y)
    b.BackgroundColor3 = Color3.fromRGB(math.random(60,150), math.random(60,150), math.random(60,150))
    b.TextColor3 = Color3.new(1, 1, 1)
    b.Text = txt
    b.Font = Enum.Font.SourceSansBold
    b.TextSize = 18
    Instance.new("UICorner", b).CornerRadius = UDim.new(0, 8)
    b.MouseButton1Click:Connect(callback)
    return b
end

local invisivel = false
local esp = false
local espObjs = {}
local fly = false
local speed = false
local cloneProp = nil

local function toggleInvisivel()
    local char = LocalPlayer.Character
    if not char then return end
    invisivel = not invisivel
    for _, v in pairs(char:GetDescendants()) do
        if v:IsA("BasePart") or v:IsA("Decal") then
            v.Transparency = invisivel and 1 or 0
            if v:IsA("BasePart") then
                v.CanCollide = not invisivel
                v.CanTouch = not invisivel
            end
        end
    end
end

local function toggleESP()
    esp = not esp
    for _, v in pairs(espObjs) do v:Destroy() end
    espObjs = {}
    if esp then
        for _, p in pairs(Players:GetPlayers()) do
            if p ~= LocalPlayer and p.Character and p.Character:FindFirstChild("HumanoidRootPart") then
                local b = Instance.new("BoxHandleAdornment")
                b.Adornee = p.Character.HumanoidRootPart
                b.AlwaysOnTop = true
                b.Size = Vector3.new(5, 6, 1)
                b.Transparency = 0.5
                b.ZIndex = 5
                b.Color3 = Color3.new(1, 0, 0)
                b.Parent = workspace
                table.insert(espObjs, b)
            end
        end
    end
end

local function toggleFly()
    local char = LocalPlayer.Character
    local hrp = char and char:FindFirstChild("HumanoidRootPart")
    if not hrp then return end
    fly = not fly
    if fly then
        local bv = Instance.new("BodyVelocity", hrp)
        bv.Name = "FlyForce"
        bv.MaxForce = Vector3.new(1e5, 1e5, 1e5)
        bv.Velocity = Vector3.zero
    else
        local f = hrp:FindFirstChild("FlyForce")
        if f then f:Destroy() end
    end
end

RunService.RenderStepped:Connect(function()
    if fly then
        local char = LocalPlayer.Character
        local hrp = char and char:FindFirstChild("HumanoidRootPart")
        if hrp then
            local dir = Vector3.zero
            if UserInputService:IsKeyDown(Enum.KeyCode.W) then
                dir = dir + workspace.CurrentCamera.CFrame.LookVector
            end
            if UserInputService:IsKeyDown(Enum.KeyCode.S) then
                dir = dir - workspace.CurrentCamera.CFrame.LookVector
            end
            if UserInputService:IsKeyDown(Enum.KeyCode.Space) then
                dir = dir + Vector3.new(0,1,0)
            end
            if UserInputService:IsKeyDown(Enum.KeyCode.LeftControl) then
                dir = dir - Vector3.new(0,1,0)
            end
            local bv = hrp:FindFirstChild("FlyForce")
            if bv and dir.Magnitude > 0 then
                bv.Velocity = dir.Unit * 50
            end
        end
    end
end)

local function toggleSpeed()
    speed = not speed
    local hum = LocalPlayer.Character and LocalPlayer.Character:FindFirstChildOfClass("Humanoid")
    if hum then
        hum.WalkSpeed = speed and 50 or 16
    end
end

local function cloneCharacter()
    if cloneProp then cloneProp:Destroy() end
    local char = LocalPlayer.Character
    if not char then return end
    local c = char:Clone()
    c.Name = "FakeNey"
    for _, s in pairs(c:GetDescendants()) do
        if s:IsA("Script") or s:IsA("LocalScript") then s:Destroy() end
    end
    c.Parent = workspace
    cloneProp = c
end

criarBotao("Invisibilidade", 60, toggleInvisivel)
criarBotao("ESP Players", 110, toggleESP)
criarBotao("Fly", 160, toggleFly)
criarBotao("SpeedHack", 210, toggleSpeed)
criarBotao("Clonar Personagem", 260, cloneCharacter)

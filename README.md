local Players = game:GetService("Players")
local UserInputService = game:GetService("UserInputService")
local RunService = game:GetService("RunService")
local Workspace = workspace

local LocalPlayer = Players.LocalPlayer
local Camera = Workspace.CurrentCamera

local State = {
    Flying = false,
    SpeedOn = false,
    JumpOn = false,
    ESPOn = false,
    AimOn = false,
    Firing = false,
    FlySpeed = 60,
    WalkSpeed = 60,
    JumpPower = 50,
    MinRange = 20,
    MaxRange = 300,
    AimRange = 120,
    CurrentTarget = nil,
    CurrentDistance = math.huge,
}

-- GUI Initialization
local Gui = Instance.new("ScreenGui")
Gui.Name = "VanhOptimized"
Gui.ResetOnSpawn = false
Gui.Parent = LocalPlayer:WaitForChild("PlayerGui")

local ESPFolder = Instance.new("Folder")
ESPFolder.Name = "VanhESPContainer"
ESPFolder.Parent = Gui


local AimCircle = Instance.new("Frame")
AimCircle.Name = "AimCircle"
AimCircle.AnchorPoint = Vector2.new(0.5, 0.5)
AimCircle.Position = UDim2.fromScale(0.5, 0.5)
AimCircle.Size = UDim2.fromOffset(State.AimRange * 2, State.AimRange * 2)
AimCircle.BackgroundTransparency = 1
AimCircle.Visible = false
AimCircle.Parent = Gui

local CircleCorner = Instance.new("UICorner")
CircleCorner.CornerRadius = UDim.new(1, 0)
CircleCorner.Parent = AimCircle

local CircleStroke = Instance.new("UIStroke")
CircleStroke.Thickness = 2
CircleStroke.Color = Color3.fromRGB(80, 170, 255)
CircleStroke.Parent = AimCircle

local OpenButton = Instance.new("TextButton")
OpenButton.Size = UDim2.new(0, 85, 0, 42)
OpenButton.Position = UDim2.new(0, 20, 0.5, -21)
OpenButton.BackgroundColor3 = Color3.fromRGB(25, 25, 30)
OpenButton.Text = "VANH"
OpenButton.TextColor3 = Color3.fromRGB(255, 255, 255)
OpenButton.TextSize = 18
OpenButton.Font = Enum.Font.GothamBold
OpenButton.Parent = Gui

Instance.new("UICorner", OpenButton).CornerRadius = UDim.new(0, 10)

local MainFrame = Instance.new("Frame")
MainFrame.Size = UDim2.new(0, 330, 0, 430)
MainFrame.Position = UDim2.new(0.5, -165, 0.5, -215)
MainFrame.BackgroundColor3 = Color3.fromRGB(20, 20, 25)
MainFrame.Visible = false
MainFrame.Parent = Gui

Instance.new("UICorner", MainFrame).CornerRadius = UDim.new(0, 15)


local TopBar = Instance.new("Frame")
TopBar.Size = UDim2.new(1, 0, 0, 55)
TopBar.BackgroundColor3 = Color3.fromRGB(28, 28, 34)
TopBar.Parent = MainFrame

Instance.new("UICorner", TopBar).CornerRadius = UDim.new(0, 15)

local TitleLabel = Instance.new("TextLabel")
TitleLabel.Size = UDim2.new(1, -100, 1, 0)
TitleLabel.Position = UDim2.new(0, 18, 0, 0)
TitleLabel.BackgroundTransparency = 1
TitleLabel.Text = "VANH v2.1"
TitleLabel.TextColor3 = Color3.fromRGB(255, 255, 255)
TitleLabel.TextSize = 22
TitleLabel.Font = Enum.Font.GothamBold
TitleLabel.TextXAlignment = Enum.TextXAlignment.Left
TitleLabel.Parent = TopBar

local CloseButton = Instance.new("TextButton")
CloseButton.Size = UDim2.new(0, 35, 0, 35)
CloseButton.Position = UDim2.new(1, -45, 0, 10)
CloseButton.BackgroundColor3 = Color3.fromRGB(180, 55, 55)
CloseButton.Text = "X"
CloseButton.TextColor3 = Color3.fromRGB(255, 255, 255)
CloseButton.TextSize = 16
CloseButton.Font = Enum.Font.GothamBold
CloseButton.Parent = TopBar

Instance.new("UICorner", CloseButton).CornerRadius = UDim.new(0, 9)

local SettingsTabBtn = Instance.new("TextButton")
SettingsTabBtn.Size = UDim2.new(0, 140, 0, 38)
SettingsTabBtn.Position = UDim2.new(0, 20, 0, 70)
SettingsTabBtn.BackgroundColor3 = Color3.fromRGB(55, 55, 65)
SettingsTabBtn.Text = "SETTINGS"
SettingsTabBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
SettingsTabBtn.TextSize = 14
SettingsTabBtn.Font = Enum.Font.GothamBold
SettingsTabBtn.Parent = MainFrame

Instance.new("UICorner", SettingsTabBtn).CornerRadius = UDim.new(0, 9)

local AimTabBtn = Instance.new("TextButton")
AimTabBtn.Size = UDim2.new(0, 140, 0, 38)
AimTabBtn.Position = UDim2.new(0, 170, 0, 70)
AimTabBtn.BackgroundColor3 = Color3.fromRGB(35, 35, 42)
AimTabBtn.Text = "AIM"
AimTabBtn.TextColor3 = Color3.fromRGB(180, 180, 190)
AimTabBtn.TextSize = 14
AimTabBtn.Font = Enum.Font.GothamBold
AimTabBtn.Parent = MainFrame

Instance.new("UICorner", AimTabBtn).CornerRadius = UDim.new(0, 9)

local SettingsPage = Instance.new("Frame")
SettingsPage.Size = UDim2.new(1, -40, 1, -125)
SettingsPage.Position = UDim2.new(0, 20, 0, 120)
SettingsPage.BackgroundTransparency = 1
SettingsPage.Parent = MainFrame

local AimPage = Instance.new("Frame")
AimPage.Size = UDim2.new(1, -40, 1, -125)
AimPage.Position = UDim2.new(0, 20, 0, 120)
AimPage.BackgroundTransparency = 1
AimPage.Visible = false
AimPage.Parent = MainFrame

local function CreateButton(text, y, parent)
    local b = Instance.new("TextButton")
    b.Size = UDim2.new(0, 100, 0, 38)
    b.Position = UDim2.new(0, 0, 0, y)
    b.BackgroundColor3 = Color3.fromRGB(90, 90, 100)
    b.Text = text
    b.TextColor3 = Color3.fromRGB(255, 255, 255)
    b.TextSize = 12
    b.Font = Enum.Font.GothamBold
    b.Parent = parent
    Instance.new("UICorner", b).CornerRadius = UDim.new(0, 9)
    return b
end

local function CreateBox(value, y, parent)
    local b = Instance.new("TextBox")
    b.Size = UDim2.new(0, 165, 0, 38)
    b.Position = UDim2.new(0, 125, 0, y)
    b.BackgroundColor3 = Color3.fromRGB(35, 35, 42)
    b.Text = tostring(value)
    b.TextColor3 = Color3.fromRGB(255, 255, 255)
    b.TextSize = 14
    b.Font = Enum.Font.Gotham
    b.ClearTextOnFocus = false
    b.Parent = parent
    Instance.new("UICorner", b).CornerRadius = UDim.new(0, 9)
    return b
end

local FlyButton = CreateButton("FLY OFF", 0, SettingsPage)
local FlyBox = CreateBox(State.FlySpeed, 0, SettingsPage)
local SpeedButton = CreateButton("SPEED OFF", 55, SettingsPage)
local SpeedBox = CreateBox(State.WalkSpeed, 55, SettingsPage)
local JumpButton = CreateButton("JUMP OFF", 110, SettingsPage)
local JumpBox = CreateBox(State.JumpPower, 110, SettingsPage)
local ESPButton = CreateButton("ESP OFF", 165, SettingsPage)

local function ClearESP()
    ESPFolder:ClearAllChildren()
end

local function UpdateESP()
    ClearESP()
    if not State.ESPOn then return end
    for _, p in ipairs(Players:GetPlayers()) do
        if p ~= LocalPlayer then
            local char = p.Character
            local hum = char and char:FindFirstChildOfClass("Humanoid")
            local head = char and char:FindFirstChild("Head")
            if hum and hum.Health > 0 then
                local h = Instance.new("Highlight")
                h.Adornee = char
                h.FillTransparency = 0.65
                h.OutlineTransparency = 0
                h.Parent = ESPFolder
                if head then
                    local bb = Instance.new("BillboardGui")
                    bb.Adornee = head
                    bb.Size = UDim2.new(0, 220, 0, 45)
                    bb.StudsOffset = Vector3.new(0, 3, 0)
                    bb.AlwaysOnTop = true
                    bb.Parent = ESPFolder
                    local label = Instance.new("TextLabel")
                    label.Size = UDim2.fromScale(1, 1)
                    label.BackgroundTransparency = 1
                    label.Text = p.DisplayName
                    label.TextColor3 = Color3.fromRGB(255, 255, 255)
                    label.TextStrokeTransparency = 0
                    label.TextSize = 14
                    label.Font = Enum.Font.GothamBold
                    label.Parent = bb
                end
            end
        end
    end
end

local AimTitle = Instance.new("TextLabel")
AimTitle.Size = UDim2.new(1, 0, 0, 30)
AimTitle.Position = UDim2.new(0, 0, 0, 5)
AimTitle.BackgroundTransparency = 1
AimTitle.Text = "AIM • AUTO LOCK SELECTOR"
AimTitle.TextColor3 = Color3.fromRGB(150, 150, 160)
AimTitle.TextSize = 16
AimTitle.Font = Enum.Font.GothamBold
AimTitle.Parent = AimPage

local AimStatus = Instance.new("TextLabel")
AimStatus.Size = UDim2.new(1, 0, 0, 60)
AimStatus.Position = UDim2.new(0, 0, 0, 38)
AimStatus.BackgroundTransparency = 1
AimStatus.Text = "Status: OFF\nTarget: NONE"
AimStatus.TextColor3 = Color3.fromRGB(180, 180, 190)
AimStatus.TextSize = 12
AimStatus.Font = Enum.Font.Gotham
AimStatus.TextXAlignment = Enum.TextXAlignment.Left
AimStatus.TextYAlignment = Enum.TextYAlignment.Top
AimStatus.Parent = AimPage

local RangeText = Instance.new("TextLabel")
RangeText.Size = UDim2.new(1, 0, 0, 25)
RangeText.Position = UDim2.new(0, 0, 0, 90)
RangeText.BackgroundTransparency = 1
RangeText.Text = "AIM RANGE: " .. State.AimRange
RangeText.TextColor3 = Color3.fromRGB(255, 255, 255)
RangeText.TextSize = 13
RangeText.Font = Enum.Font.GothamBold
RangeText.TextXAlignment = Enum.TextXAlignment.Left
RangeText.Parent = AimPage

local Slider = Instance.new("Frame")
Slider.Size = UDim2.new(1, 0, 0, 8)
Slider.Position = UDim2.new(0, 0, 0, 120)
Slider.BackgroundColor3 = Color3.fromRGB(55, 55, 65)
Slider.Parent = AimPage
Instance.new("UICorner", Slider).CornerRadius = UDim.new(1, 0)

local Fill = Instance.new("Frame")
Fill.Size = UDim2.new((State.AimRange - State.MinRange) / (State.MaxRange - State.MinRange), 0, 1, 0)
Fill.BackgroundColor3 = Color3.fromRGB(80, 170, 255)
Fill.Parent = Slider
Instance.new("UICorner", Fill).CornerRadius = UDim.new(1, 0)

local Knob = Instance.new("TextButton")
Knob.Size = UDim2.new(0, 18, 0, 18)
Knob.AnchorPoint = Vector2.new(0.5, 0.5)
Knob.Position = UDim2.new((State.AimRange - State.MinRange) / (State.MaxRange - State.MinRange), 0, 0.5, 0)
Knob.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
Knob.Text = ""
Knob.Parent = Slider
Instance.new("UICorner", Knob).CornerRadius = UDim.new(1, 0)

local SliderDragging = false
local function SetRange(x)
    local alpha = math.clamp((x - Slider.AbsolutePosition.X) / Slider.AbsoluteSize.X, 0, 1)
    State.AimRange = math.floor(State.MinRange + (State.MaxRange - State.MinRange) * alpha)
    local percent = (State.AimRange - State.MinRange) / (State.MaxRange - State.MinRange)
    Fill.Size = UDim2.new(percent, 0, 1, 0)
    Knob.Position = UDim2.new(percent, 0, 0.5, 0)
    RangeText.Text = "AIM RANGE: " .. State.AimRange
    AimCircle.Size = UDim2.fromOffset(State.AimRange * 2, State.AimRange * 2)
end

Knob.MouseButton1Down:Connect(function() SliderDragging = true end)
Slider.InputBegan:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseButton1 then
        SliderDragging = true
        SetRange(input.Position.X)
    end
end)

UserInputService.InputChanged:Connect(function(input)
    if SliderDragging and input.UserInputType == Enum.UserInputType.MouseMovement then
        SetRange(input.Position.X)
    end
end)

UserInputService.InputEnded:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseButton1 then
        SliderDragging = false
    end
end)

local AimButton = Instance.new("TextButton")
AimButton.Size = UDim2.new(1, 0, 0, 40)
AimButton.Position = UDim2.new(0, 0, 0, 150)
AimButton.BackgroundColor3 = Color3.fromRGB(90, 90, 100)
AimButton.Text = "AIM OFF"
AimButton.TextColor3 = Color3.fromRGB(255, 255, 255)
AimButton.TextSize = 14
AimButton.Font = Enum.Font.GothamBold
AimButton.Parent = AimPage
Instance.new("UICorner", AimButton).CornerRadius = UDim.new(0, 9)
local function IsEnemy(p)
    if p == LocalPlayer or not p:IsA("Player") then return false end
    if LocalPlayer.Team and p.Team and LocalPlayer.Team == p.Team then return false end
    local char = p.Character
    local hum = char and char:FindFirstChildOfClass("Humanoid")
    local root = char and char:FindFirstChild("HumanoidRootPart")
    return hum and root and hum.Health > 0
end

local function GetNearestTarget()
    local char = LocalPlayer.Character
    local root = char and char:FindFirstChild("HumanoidRootPart")
    if not root then return nil, math.huge end
    local best, bestDistance = nil, State.AimRange
    for _, p in ipairs(Players:GetPlayers()) do
        if IsEnemy(p) then
            local targetChar = p.Character
            local targetRoot = targetChar and targetChar:FindFirstChild("HumanoidRootPart")
            if targetRoot then
                local dist = (targetRoot.Position - root.Position).Magnitude
                if dist <= bestDistance then
                    best = p
                    bestDistance = dist
                end
            end
        end
    end
    return best, bestDistance
end

local function UpdateTarget()
    if not State.AimOn then
        State.CurrentTarget = nil
        State.CurrentDistance = math.huge
        AimStatus.Text = "Status: OFF\nTarget: NONE"
        return
    end
    local target, dist = GetNearestTarget()
    State.CurrentTarget = target
    State.CurrentDistance = dist
    if target then
        AimStatus.Text = string.format("Status: ON\nTarget: %s\nDistance: %d", target.DisplayName, math.floor(dist))
    else
        AimStatus.Text = "Status: ON\nTarget: NONE"
    end
end

AimButton.MouseButton1Click:Connect(function()
    State.AimOn = not State.AimOn
    AimCircle.Visible = State.AimOn
    if State.AimOn then
        AimButton.Text = "AIM ON"
        AimButton.BackgroundColor3 = Color3.fromRGB(45, 170, 90)
    else
        AimButton.Text = "AIM OFF"
        AimButton.BackgroundColor3 = Color3.fromRGB(90, 90, 100)
        State.CurrentTarget = nil
    end
end)

SettingsTabBtn.MouseButton1Click:Connect(function()
    SettingsPage.Visible = true
    AimPage.Visible = false
    SettingsTabBtn.BackgroundColor3 = Color3.fromRGB(55, 55, 65)
    AimTabBtn.BackgroundColor3 = Color3.fromRGB(35, 35, 42)
end)

AimTabBtn.MouseButton1Click:Connect(function()
    SettingsPage.Visible = false
    AimPage.Visible = true
    SettingsTabBtn.BackgroundColor3 = Color3.fromRGB(35, 35, 42)
    AimTabBtn.BackgroundColor3 = Color3.fromRGB(55, 55, 65)
end)

OpenButton.MouseButton1Click:Connect(function()
    MainFrame.Visible = not MainFrame.Visible
end)

FlyButton.MouseButton1Click:Connect(function()
    State.Flying = not State.Flying
    if State.Flying then
        FlyButton.Text = "FLY ON"
        FlyButton.BackgroundColor3 = Color3.fromRGB(45, 170, 90)
    else
        FlyButton.Text = "FLY OFF"
        FlyButton.BackgroundColor3 = Color3.fromRGB(90, 90, 100)
        local char = LocalPlayer.Character
        local root = char and char:FindFirstChild("HumanoidRootPart")
        local bv = root and root:FindFirstChild("VanhFly")
        if bv then bv:Destroy() end
    end
end)

SpeedButton.MouseButton1Click:Connect(function()
    State.SpeedOn = not State.SpeedOn
    local hum = LocalPlayer.Character and LocalPlayer.Character:FindFirstChildOfClass("Humanoid")
    if State.SpeedOn then
        SpeedButton.Text = "SPEED ON"
        SpeedButton.BackgroundColor3 = Color3.fromRGB(45, 170, 90)
        if hum then hum.WalkSpeed = State.WalkSpeed end
    else
        SpeedButton.Text = "SPEED OFF"
        SpeedButton.BackgroundColor3 = Color3.fromRGB(90, 90, 100)
        if hum then hum.WalkSpeed = 16 end
    end
end)

JumpButton.MouseButton1Click:Connect(function()
    State.JumpOn = not State.JumpOn
    local hum = LocalPlayer.Character and LocalPlayer.Character:FindFirstChildOfClass("Humanoid")
    if State.JumpOn then
        JumpButton.Text = "JUMP ON"
        JumpButton.BackgroundColor3 = Color3.fromRGB(45, 170, 90)
        if hum then
            hum.UseJumpPower = true
            hum.JumpPower = State.JumpPower
        end
    else
        JumpButton.Text = "JUMP OFF"
        JumpButton.BackgroundColor3 = Color3.fromRGB(90, 90, 100)
        if hum then
            hum.UseJumpPower = true
            hum.JumpPower = 50
        end
    end
end)

ESPButton.MouseButton1Click:Connect(function()
    State.ESPOn = not State.ESPOn
    if State.ESPOn then
        ESPButton.Text = "ESP ON"
        ESPButton.BackgroundColor3 = Color3.fromRGB(45, 170, 90)
        UpdateESP()
    else
        ESPButton.Text = "ESP OFF"
        ESPButton.BackgroundColor3 = Color3.fromRGB(90, 90, 100)
        ClearESP()
    end
end)

FlyBox.FocusLost:Connect(function()
    local v = tonumber(FlyBox.Text)
    if v and v > 0 then State.FlySpeed = v else FlyBox.Text = tostring(State.FlySpeed) end
end)

SpeedBox.FocusLost:Connect(function()
    local v = tonumber(SpeedBox.Text)
    if v and v >= 0 then State.WalkSpeed = v else SpeedBox.Text = tostring(State.WalkSpeed) end
end)

JumpBox.FocusLost:Connect(function()
    local v = tonumber(JumpBox.Text)
    if v and v >= 0 then State.JumpPower = v else JumpBox.Text = tostring(State.JumpPower) end
end)

RunService.RenderStepped:Connect(function()
    if AimCircle.Visible then
        AimCircle.Position = UDim2.fromOffset(Camera.ViewportSize.X / 2, Camera.ViewportSize.Y / 2)
        AimCircle.Size = UDim2.fromOffset(State.AimRange * 2, State.AimRange * 2)
    end
    UpdateTarget()
    if State.AimOn and State.CurrentTarget then
        local targetChar = State.CurrentTarget.Character
        local targetRoot = targetChar and targetChar:FindFirstChild("HumanoidRootPart")
        if targetRoot then
            Camera.CFrame = CFrame.new(Camera.CFrame.Position, targetRoot.Position)
        end
    end
    if State.Flying then
        local char = LocalPlayer.Character
        local root = char and char:FindFirstChild("HumanoidRootPart")
        if root then
            local bv = root:FindFirstChild("VanhFly")
            if not bv then
                bv = Instance.new("BodyVelocity")
                bv.Name = "VanhFly"
                bv.MaxForce = Vector3.new(math.huge, math.huge, math.huge)
                bv.Parent = root
            end
            local move = Vector3.zero
            if UserInputService:IsKeyDown(Enum.KeyCode.W) then move += Camera.CFrame.LookVector end
            if UserInputService:IsKeyDown(Enum.KeyCode.S) then move -= Camera.CFrame.LookVector end
            if UserInputService:IsKeyDown(Enum.KeyCode.A) then move -= Camera.CFrame.RightVector end
            if UserInputService:IsKeyDown(Enum.KeyCode.D) then move += Camera.CFrame.RightVector end
            if UserInputService:IsKeyDown(Enum.KeyCode.Space) then move += Vector3.new(0, 1, 0) end
            if UserInputService:IsKeyDown(Enum.KeyCode.LeftControl) then move -= Vector3.new(0, 1, 0) end
            bv.Velocity = move.Magnitude > 0 and move.Unit * State.FlySpeed or Vector3.zero
        end
    end
end)

task.spawn(function()
    while Gui.Parent do
        if State.ESPOn then
            UpdateESP()
        end
        task.wait(0.5)
    end
end)

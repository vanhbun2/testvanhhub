local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local TweenService = game:GetService("TweenService")
local Workspace = game:GetService("Workspace")
local VirtualUser = game:GetService("VirtualUser")

local player = Players.LocalPlayer


local SETTINGS = {
    DefaultSpeed = 16,
    DefaultJump = 50,
    DefaultAttackDelay = 0.05,
    FarmHeight = 5,
    FlySpeed = 250,
}

local state = {
    open = true,
    autoFarm = false,
    noclip = false,
    hitboxExpand = true,
    weapon = "Melee",
    attackDelay = SETTINGS.DefaultAttackDelay,
}

local statusLabel
local activeTween = nil

local function character()
    return player.Character or player.CharacterAdded:Wait()
end

local function humanoid()
    local c = character()
    return c and c:FindFirstChildOfClass("Humanoid")
end

local function root()
    local c = character()
    return c and c:FindFirstChild("HumanoidRootPart")
end

local function setStatus(text)
    if statusLabel and statusLabel.Parent then
        statusLabel.Text = "Status: " .. text
    end
end

-- Tối ưu di chuyển mượt
local function smoothMoveTo(targetCFrame, speed)
    local r = root()
    if not r then return end
    local distance = (r.Position - targetCFrame.Position).Magnitude
    if distance < 2 then return end
    local timeToTravel = distance / (speed or SETTINGS.FlySpeed)
    local tweenInfo = TweenInfo.new(
        timeToTravel,
        Enum.EasingStyle.Linear,
        Enum.EasingDirection.Out
    )
    if activeTween then activeTween:Cancel() end
    activeTween = TweenService:Create(r, tweenInfo, {CFrame = targetCFrame})
    activeTween:Play()
    return activeTween
end

local function getFolder(name)
    return Workspace:FindFirstChild(name)
end

-- Trang bị vũ khí
local function equipWeapon()
    local c = character()
    local h = humanoid()
    if not c or not h then return end
    for _, tool in ipairs(c:GetChildren()) do
        if tool:IsA("Tool") then
            return
        end
    end
    local backpack = player:FindFirstChildOfClass("Backpack")
    if not backpack then return end
    for _, tool in ipairs(backpack:GetChildren()) do
        if tool:IsA("Tool") then
            local name = tool.Name:lower()
            local target = state.weapon:lower()
            if name:find(target) or (target == "melee" and (name:find("combat") or name:find("fist"))) then
                h:EquipTool(tool)
                break
            end
        end
    end
end

-- Đánh tự động
local function attackTarget()
    local c = character()
    if not c then return end
    pcall(function()
        VirtualUser:Button1Down(Vector2.new(0,0), workspace.CurrentCamera.CFrame)
        VirtualUser:Button1Up(Vector2.new(0,0), workspace.CurrentCamera.CFrame)
        for _, tool in ipairs(c:GetChildren()) do
            if tool:IsA("Tool") then
                tool:Activate()
            end
        end
    end)
end

-- Mở rộng Hitbox NPC
local function expandNPC(npc)
    if not state.hitboxExpand or not npc then return end
    local nr = npc:FindFirstChild("HumanoidRootPart")
    if nr then
        nr.Size = Vector3.new(12, 12, 12)
        nr.Transparency = 0.8
        nr.CanCollide = false
    end
end

-- Tìm NPC gần nhất
local function nearestNPC()
    local r = root()
    local folder = getFolder("TestNPCs") or getFolder("Enemies") or getFolder("Mobs")
    if not r or not folder then return nil end
    local best, bestDistance
    for _, npc in ipairs(folder:GetChildren()) do
        local nr = npc:FindFirstChild("HumanoidRootPart")
        local nh = npc:FindFirstChildOfClass("Humanoid")
        if nr and nh and nh.Health > 0 then
            local d = (nr.Position - r.Position).Magnitude
            if not bestDistance or d < bestDistance then
                best = npc
                bestDistance = d
            end
        end
    end
    return best
end

-- GUI
local gui = Instance.new("ScreenGui")
gui.Name = "VanhHubPro"
gui.ResetOnSpawn = false
gui.ZIndexBehavior = Enum.ZIndexBehavior.Sibling
gui.Parent = player:WaitForChild("PlayerGui")

local toggle = Instance.new("TextButton")
toggle.Name = "VanhToggle"
toggle.Size = UDim2.fromOffset(90, 42)
toggle.Position = UDim2.new(0, 20, 0.5, -21)
toggle.BackgroundColor3 = Color3.fromRGB(35, 35, 42)
toggle.TextColor3 = Color3.fromRGB(0, 255, 170)
toggle.Text = "⚡ VANH PRO"
toggle.TextSize = 14
toggle.Font = Enum.Font.GothamBold
toggle.Parent = gui

local toggleCorner = Instance.new("UICorner")
toggleCorner.CornerRadius = UDim.new(0, 8)
toggleCorner.Parent = toggle

local main = Instance.new("Frame")
main.Name = "Main"
main.Size = UDim2.fromOffset(480, 420)
main.Position = UDim2.new(0.5, -240, 0.5, -210)
main.BackgroundColor3 = Color3.fromRGB(20, 20, 24)
main.BorderSizePixel = 0
main.Parent = gui

local mainCorner = Instance.new("UICorner")
mainCorner.CornerRadius = UDim.new(0, 10)
mainCorner.Parent = main

local header = Instance.new("TextLabel")
header.Size = UDim2.new(1, -30, 0, 50)
header.Position = UDim2.fromOffset(15, 5)
header.BackgroundTransparency = 1
header.Text = "Vanh Hub — Ultra Edition"
header.TextColor3 = Color3.fromRGB(255, 255, 255)
header.TextSize = 20
header.Font = Enum.Font.GothamBold
header.TextXAlignment = Enum.TextXAlignment.Left
header.Parent = main

local close = Instance.new("TextButton")
close.Size = UDim2.fromOffset(30, 30)
close.Position = UDim2.new(1, -40, 0, 12)
close.BackgroundColor3 = Color3.fromRGB(40, 40, 48)
close.Text = "×"
close.TextColor3 = Color3.fromRGB(255, 255, 255)
close.TextSize = 20
close.Font = Enum.Font.GothamBold
close.Parent = main

local closeCorner = Instance.new("UICorner")
closeCorner.CornerRadius = UDim.new(0, 6)
closeCorner.Parent = close

statusLabel = Instance.new("TextLabel")
statusLabel.Size = UDim2.new(1, -30, 0, 28)
statusLabel.Position = UDim2.fromOffset(15, 55)
statusLabel.BackgroundColor3 = Color3.fromRGB(28, 28, 34)
statusLabel.Text = "Status: Ready"
statusLabel.TextColor3 = Color3.fromRGB(0, 255, 150)
statusLabel.TextSize = 12
statusLabel.Font = Enum.Font.GothamMedium
statusLabel.Parent = main

local statusCorner = Instance.new("UICorner")
statusCorner.CornerRadius = UDim.new(0, 6)
statusCorner.Parent = statusLabel

local scroll = Instance.new("ScrollingFrame")
scroll.Size = UDim2.new(1, -30, 1, -100)
scroll.Position = UDim2.fromOffset(15, 90)
scroll.BackgroundTransparency = 1
scroll.BorderSizePixel = 0
scroll.ScrollBarThickness = 4
scroll.CanvasSize = UDim2.new()
scroll.AutomaticCanvasSize = Enum.AutomaticSize.Y
scroll.Parent = main

local layout = Instance.new("UIListLayout")
layout.Padding = UDim.new(0, 8)
layout.SortOrder = Enum.SortOrder.LayoutOrder
layout.Parent = scroll

local function button(text)
    local b = Instance.new("TextButton")
    b.Size = UDim2.new(1, -4, 0, 38)
    b.BackgroundColor3 = Color3.fromRGB(32, 32, 38)
    b.TextColor3 = Color3.fromRGB(240, 240, 240)
    b.Text = text
    b.TextSize = 13
    b.Font = Enum.Font.GothamMedium
    b.Parent = scroll
    local c = Instance.new("UICorner")
    c.CornerRadius = UDim.new(0, 6)
    c.Parent = b
    return b
end

-- Nút điều khiển
local chooseButton = button("⚔️ Weapon: Melee")
chooseButton.MouseButton1Click:Connect(function()
    state.weapon = (state.weapon == "Melee") and "Sword" or "Melee"
    chooseButton.Text = "⚔️ Weapon: " .. state.weapon
    setStatus("Weapon set to " .. state.weapon)
end)

local farmButton = button("🤖 Auto Farm: OFF")
farmButton.MouseButton1Click:Connect(function()
    state.autoFarm = not state.autoFarm
    farmButton.Text = "🤖 Auto Farm: " .. (state.autoFarm and "ON" or "OFF")
    setStatus(state.autoFarm and "Auto Farm Enabled" or "Auto Farm Disabled")
end)

local hitboxButton = button("🎯 Expand Hitbox: ON")
hitboxButton.MouseButton1Click:Connect(function()
    state.hitboxExpand = not state.hitboxExpand
    hitboxButton.Text = "🎯 Expand Hitbox: " .. (state.hitboxExpand and "ON" or "OFF")
end)

local noclipButton = button("👻 Noclip: OFF")
noclipButton.MouseButton1Click:Connect(function()
    state.noclip = not state.noclip
    noclipButton.Text = "👻 Noclip: " .. (state.noclip and "ON" or "OFF")
    setStatus(state.noclip and "Noclip Enabled" or "Noclip Disabled")
end)

-- Vòng lặp Auto Farm
task.spawn(function()
    while gui.Parent do
        task.wait(state.attackDelay)
        if state.autoFarm then
            pcall(function()
                local r = root()
                local npc = nearestNPC()
                if r and npc then
                    local nr = npc:FindFirstChild("HumanoidRootPart")
                    local nh = npc:FindFirstChildOfClass("Humanoid")
                    if nr and nh and nh.Health > 0 then
                        equipWeapon()
                        expandNPC(npc)
                        local targetCFrame = nr.CFrame * CFrame.new(0, SETTINGS.FarmHeight, 0) * CFrame.Angles(math.rad(-90), 0, 0)
                        smoothMoveTo(targetCFrame, SETTINGS.FlySpeed)
                        attackTarget()
                        setStatus("Farming: " .. npc.Name .. " [" .. math.floor(nh.Health) .. " HP]")
                    end
                else
                    setStatus("Searching for NPCs...")
                end
            end)
        end
    end
end)

-- Vòng lặp Noclip
RunService.Stepped:Connect(function()
    if state.noclip then
        local c = character()
        if c then
            for _, part in ipairs(c:GetDescendants()) do
                if part:IsA("BasePart") then
                    part.CanCollide = false
                end
            end
        end
    end
end)

local function setOpen(value)
    state.open = value
    main.Visible = value
end

toggle.MouseButton1Click:Connect(function() setOpen(not state.open) end)
close.MouseButton1Click:Connect(function() setOpen(false) end)

setStatus("VanhHub Loaded!")

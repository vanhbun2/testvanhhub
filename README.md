local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local UserInputService = game:GetService("UserInputService")
local TweenService = game:GetService("TweenService")
local Workspace = game:GetService("Workspace")
local player = Players.LocalPlayer

local SETTINGS = {
    DefaultSpeed = 16,
    DefaultJump = 50,
    DefaultAttackDelay = 0.1,
    FruitCheckDistance = 5000,
    FarmHeight = 4.5,
    FlySpeed = 120, -- Tốc độ bay mượt
}

local state = {
    open = true,
    autoFarm = false,
    teleFruit = false,
    noclip = false,
    weapon = "Melee",
    speed = SETTINGS.DefaultSpeed,
    jump = SETTINGS.DefaultJump,
    attackDelay = SETTINGS.DefaultAttackDelay,
}

local statusLabel

local function character()
    return player.Character
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
    if statusLabel then
        statusLabel.Text = "Status: " .. text
    end
end

local function smoothMoveTo(targetCFrame, speed)
    local r = root()
    if not r then return end
    local distance = (r.Position - targetCFrame.Position).Magnitude
    local timeToTravel = distance / (speed or SETTINGS.FlySpeed)
    local tweenInfo = TweenInfo.new(
        timeToTravel,
        Enum.EasingStyle.Linear,
        Enum.EasingDirection.Out
    )
    local tween = TweenService:Create(r, tweenInfo, {CFrame = targetCFrame})
    tween:Play()
    return tween
end

local function getFolder(name)
    return Workspace:FindFirstChild(name)
end

local function equipWeapon()
    local c = character()
    if not c then return end
    local backpack = player:FindFirstChildOfClass("Backpack")
    if not backpack then return end
    for _, tool in ipairs(c:GetChildren()) do
        if tool:IsA("Tool") then
            if tool.Name:lower():find(state.weapon:lower()) or (state.weapon == "Melee" and (tool.Name:lower():find("combat") or tool.Name:lower():find("melee") or tool.Name:lower():find("fist"))) then
                return
            end
        end
    end
    for _, tool in ipairs(backpack:GetChildren()) do
        if tool:IsA("Tool") then
            if tool.Name:lower():find(state.weapon:lower()) or (state.weapon == "Melee" and (tool.Name:lower():find("combat") or tool.Name:lower():find("melee") or tool.Name:lower():find("fist"))) then
                humanoid():EquipTool(tool)
                break
            end
        end
    end
end

local function attackTarget()
    local c = character()
    if not c then return end
    for _, tool in ipairs(c:GetChildren()) do
        if tool:IsA("Tool") then
            pcall(function()
                tool:Activate()
                local vim = game:GetService("VirtualUser")
                vim:Button1Down(Vector2.new(0,0), workspace.CurrentCamera.CFrame)
                vim:Button1Up(Vector2.new(0,0), workspace.CurrentCamera.CFrame)
            end)
        end
    end
end

local function expandNPC()
    local folder = getFolder("TestNPCs") or getFolder("Enemies") or getFolder("Mobs")
    if not folder then return end
    for _, npc in ipairs(folder:GetChildren()) do
        local nr = npc:FindFirstChild("HumanoidRootPart")
        local nh = npc:FindFirstChildOfClass("Humanoid")
        if nr and nh and nh.Health > 0 then
            nr.Size = Vector3.new(6, 6, 6)
            nr.Transparency = 0.7
            nr.CanCollide = false
        end
    end
end

local gui = Instance.new("ScreenGui")
gui.Name = "VanhHubFinal"
gui.ResetOnSpawn = false
gui.ZIndexBehavior = Enum.ZIndexBehavior.Sibling
gui.Parent = player:WaitForChild("PlayerGui")

local toggle = Instance.new("TextButton")
toggle.Name = "VanhToggle"
toggle.Size = UDim2.fromOffset(86, 42)
toggle.Position = UDim2.new(0, 20, 0.5, -21)
toggle.BackgroundColor3 = Color3.fromRGB(30, 30, 34)
toggle.TextColor3 = Color3.fromRGB(255, 255, 255)
toggle.Text = "⚡ VANH"
toggle.TextSize = 15
toggle.Font = Enum.Font.GothamBold
toggle.Parent = gui

local toggleCorner = Instance.new("UICorner")
toggleCorner.CornerRadius = UDim.new(0, 9)
toggleCorner.Parent = toggle

local main = Instance.new("Frame")
main.Name = "Main"
main.Size = UDim2.fromOffset(500, 540)
main.Position = UDim2.new(0.5, -250, 0.5, -270)
main.BackgroundColor3 = Color3.fromRGB(24, 24, 27)
main.BorderSizePixel = 0
main.Parent = gui

local mainCorner = Instance.new("UICorner")
mainCorner.CornerRadius = UDim.new(0, 12)
mainCorner.Parent = main

local header = Instance.new("TextLabel")
header.Size = UDim2.new(1, -30, 0, 55)
header.Position = UDim2.fromOffset(15, 8)
header.BackgroundTransparency = 1
header.Text = "Vanh Hub - Enhanced Edition"
header.TextColor3 = Color3.fromRGB(245, 245, 245)
header.TextSize = 24
header.Font = Enum.Font.GothamBold
header.TextXAlignment = Enum.TextXAlignment.Left
header.Parent = main

local close = Instance.new("TextButton")
close.Size = UDim2.fromOffset(34, 34)
close.Position = UDim2.new(1, -45, 0, 14)
close.BackgroundColor3 = Color3.fromRGB(45, 45, 50)
close.Text = "×"
close.TextColor3 = Color3.fromRGB(255, 255, 255)
close.TextSize = 22
close.Font = Enum.Font.GothamBold
close.Parent = main

local closeCorner = Instance.new("UICorner")
closeCorner.CornerRadius = UDim.new(0, 8)
closeCorner.Parent = close

statusLabel = Instance.new("TextLabel")
statusLabel.Size = UDim2.new(1, -30, 0, 30)
statusLabel.Position = UDim2.fromOffset(15, 63)
statusLabel.BackgroundColor3 = Color3.fromRGB(31, 31, 36)
statusLabel.Text = "Status: Ready"
statusLabel.TextColor3 = Color3.fromRGB(160, 255, 180)
statusLabel.TextSize = 13
statusLabel.Font = Enum.Font.Gotham
statusLabel.Parent = main

local statusCorner = Instance.new("UICorner")
statusCorner.CornerRadius = UDim.new(0, 7)
statusCorner.Parent = statusLabel

local scroll = Instance.new("ScrollingFrame")
scroll.Size = UDim2.new(1, -30, 1, -110)
scroll.Position = UDim2.fromOffset(15, 102)
scroll.BackgroundTransparency = 1
scroll.BorderSizePixel = 0
scroll.ScrollBarThickness = 5
scroll.CanvasSize = UDim2.new()
scroll.AutomaticCanvasSize = Enum.AutomaticSize.Y
scroll.Parent = main

local layout = Instance.new("UIListLayout")
layout.Padding = UDim.new(0, 8)
layout.SortOrder = Enum.SortOrder.LayoutOrder
layout.Parent = scroll

local function section(text)
    local label = Instance.new("TextLabel")
    label.Size = UDim2.new(1, -4, 0, 35)
    label.BackgroundTransparency = 1
    label.Text = text
    label.TextColor3 = Color3.fromRGB(235, 235, 235)
    label.TextSize = 18
    label.Font = Enum.Font.GothamBold
    label.TextXAlignment = Enum.TextXAlignment.Left
    label.Parent = scroll
    return label
end

local function button(text)
    local b = Instance.new("TextButton")
    b.Size = UDim2.new(1, -4, 0, 40)
    b.BackgroundColor3 = Color3.fromRGB(34, 34, 40)
    b.TextColor3 = Color3.fromRGB(245, 245, 245)
    b.Text = text
    b.TextSize = 14
    b.Font = Enum.Font.GothamMedium
    b.Parent = scroll
    local c = Instance.new("UICorner")
    c.CornerRadius = UDim.new(0, 8)
    c.Parent = b
    return b
end

section("📌 Farm Features")

local chooseButton = button("⚔️ Weapon: Melee")
chooseButton.MouseButton1Click:Connect(function()
    state.weapon = (state.weapon == "Melee") and "Sword" or "Melee"
    chooseButton.Text = "⚔️ Weapon: " .. state.weapon
    setStatus("Weapon set to " .. state.weapon)
end)

local farmButton = button("🤖 Auto Farm: OFF")

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

farmButton.MouseButton1Click:Connect(function()
    state.autoFarm = not state.autoFarm
    farmButton.Text = "🤖 Auto Farm: " .. (state.autoFarm and "ON" or "OFF")
    setStatus(state.autoFarm and "Auto Farm ON" or "Auto Farm OFF")
end)

-- Vòng lặp Auto Farm mượt hơn
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
                        expandNPC()
                        local targetCFrame = nr.CFrame * CFrame.new(0, SETTINGS.FarmHeight, 0) * CFrame.Angles(math.rad(-90), 0, 0)
                        smoothMoveTo(targetCFrame, SETTINGS.FlySpeed)
                        attackTarget()
                        setStatus("Farming: " .. npc.Name .. " [HP: " .. math.floor(nh.Health) .. "]")
                    end
                else
                    setStatus("Searching for targets...")
                end
            end)
        end
    end
end)

local fruitButton = button("🍎 Tele To Fruit: OFF")

local function nearestFruit()
    local r = root()
    local folder = getFolder("Fruits")
    if not r or not folder then return nil end
    local best, bestDistance
    for _, fruit in ipairs(folder:GetChildren()) do
        local part = fruit:IsA("BasePart") and fruit or fruit:FindFirstChildWhichIsA("BasePart", true)
        if part then
            local d = (part.Position - r.Position).Magnitude
            if d <= SETTINGS.FruitCheckDistance and (not bestDistance or d < bestDistance) then
                best = part
                bestDistance = d
            end
        end
    end
    return best
end
fruitButton.MouseButton1Click:Connect(function()
    state.teleFruit = not state.teleFruit
    fruitButton.Text = "🍎 Tele To Fruit: " .. (state.teleFruit and "ON" or "OFF")
    setStatus(state.teleFruit and "Fruit Watcher ON" or "Fruit Watcher OFF")
end)

task.spawn(function()
    while gui.Parent do
        task.wait(0.5)
        if state.teleFruit then
            pcall(function()
                local r = root()
                local fruit = nearestFruit()
                if r and fruit then
                    setStatus("Flying to fruit...")
                    local targetCFrame = fruit.CFrame + Vector3.new(0, 3, 0)
                    local tween = smoothMoveTo(targetCFrame, 150)
                    if tween then tween.Completed:Wait() end
                    setStatus("Reached fruit!")
                    state.teleFruit = false
                    fruitButton.Text = "🍎 Tele To Fruit: OFF"
                end
            end)
        end
    end
end)

section("🚀 Utilities")

local noclipButton = button("👻 Noclip: OFF")
noclipButton.MouseButton1Click:Connect(function()
    state.noclip = not state.noclip
    noclipButton.Text = "👻 Noclip: " .. (state.noclip and "ON" or "OFF")
    setStatus(state.noclip and "Noclip Enabled" or "Noclip Disabled")
end)

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

setStatus("VanhHub Enhanced loaded successfully!")

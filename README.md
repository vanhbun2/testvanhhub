local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local UserInputService = game:GetService("UserInputService")
local Workspace = game:GetService("Workspace")
local player = Players.LocalPlayer

local SETTINGS = {
    DefaultSpeed = 16,
    DefaultJump = 50,
    DefaultAttackDelay = 0.1,
    FruitCheckDistance = 5000,
    FarmHeight = 4.5, 
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

local connections = {}
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

local function getNumber(textBox, fallback)
    local n = tonumber(textBox.Text)
    if n == nil then
        return fallback
    end
    return n
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
            nr.Transparency = 0.7 hình (0.7 để dễ nhìn)
            nr.CanCollide = false
        end
    end
end
local gui = Instance.new("ScreenGui")
gui.Name = "VanhHubGodEdition"
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
header.Text = "Vanh Hub - God Edition"
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

local padding = Instance.new("UIPadding")
padding.PaddingBottom = UDim.new(0, 12)
padding.Parent = scroll

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

local function inputRow(title, value)
    local holder = Instance.new("Frame")
    holder.Size = UDim2.new(1, -4, 0, 42)
    holder.BackgroundColor3 = Color3.fromRGB(34, 34, 40)
    holder.Parent = scroll
    local c = Instance.new("UICorner")
    c.CornerRadius = UDim.new(0, 8)
    c.Parent = holder
    local label = Instance.new("TextLabel")
    label.Size = UDim2.new(0.58, 0, 1, 0)
    label.BackgroundTransparency = 1
    label.Text = title
    label.TextColor3 = Color3.fromRGB(235, 235, 235)
    label.TextSize = 14
    label.Font = Enum.Font.GothamMedium
    label.TextXAlignment = Enum.TextXAlignment.Left
    label.Parent = holder
    local box = Instance.new("TextBox")
    box.Size = UDim2.new(0.34, 0, 0, 30)
    box.Position = UDim2.new(0.64, 0, 0.5, -15)
    box.BackgroundColor3 = Color3.fromRGB(22, 22, 26)
    box.TextColor3 = Color3.fromRGB(255, 255, 255)
    box.PlaceholderColor3 = Color3.fromRGB(120, 120, 125)
    box.Text = tostring(value)
    box.ClearTextOnFocus = false
    box.TextSize = 13
    box.Font = Enum.Font.Gotham
    box.Parent = holder
    local bc = Instance.new("UICorner")
    bc.CornerRadius = UDim.new(0, 6)
    bc.Parent = box
    return box
end
section("📌 Farm Features")

local chooseButton = button("⚔️ Weapon: Melee")
chooseButton.MouseButton1Click:Connect(function()
    if state.weapon == "Melee" then
        state.weapon = "Sword"
    else
        state.weapon = "Melee"
    end
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
                        expandNPC() -- Tự động mở rộng hitbox quái liên tục
                        r.CFrame = nr.CFrame * CFrame.new(0, SETTINGS.FarmHeight, 0) * CFrame.Angles(math.rad(90), 0, 0)
                        attackTarget()
                        setStatus("Farming from above: " .. npc.Name .. " [HP: " .. math.floor(nh.Health) .. "]")
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
                    r.CFrame = fruit.CFrame + Vector3.new(0, 3, 0)
                    setStatus("Teleported to fruit!")
                    state.teleFruit = false
                    fruitButton.Text = "🍎 Tele To Fruit: OFF"
                end
            end)
        end
    end
end)
section("⚙️ Character Settings")

local speedBox = inputRow("WalkSpeed", SETTINGS.DefaultSpeed)
local jumpBox = inputRow("JumpPower", SETTINGS.DefaultJump)
local attackBox = inputRow("Attack Delay", SETTINGS.DefaultAttackDelay)

local applyButton = button("✓ Apply Settings")
applyButton.MouseButton1Click:Connect(function()
    state.speed = math.clamp(getNumber(speedBox, SETTINGS.DefaultSpeed), 0, 300)
    state.jump = math.clamp(getNumber(jumpBox, SETTINGS.DefaultJump), 0, 300)
    state.attackDelay = math.clamp(getNumber(attackBox, SETTINGS.DefaultAttackDelay), 0.05, 5)
    local h = humanoid()
    if h then
        h.WalkSpeed = state.speed
        if h.UseJumpPower then
            h.JumpPower = state.jump
        else
            h.JumpHeight = math.clamp(state.jump / 7, 0, 50)
        end
    end
    setStatus("Settings applied successfully!")
end)

-- Khóa cứng tốc độ chạy hoàn toàn
RunService.Heartbeat:Connect(function()
    local h = humanoid()
    local r = root()
    if h then
        if h.WalkSpeed ~= state.speed then
            h.WalkSpeed = state.speed
        end
    end
    if r and h and h.MoveDirection.Magnitude > 0 and state.speed > 16 then
        local currentVelocity = r.AssemblyLinearVelocity
        r.AssemblyLinearVelocity = Vector3.new(h.MoveDirection.X * state.speed, currentVelocity.Y, h.MoveDirection.Z * state.speed)
    end
end)
section("🚀 Performance & Utilities")

local fpsLabel = button("FPS: --")
fpsLabel.Active = false

local noclipButton = button("👻 Noclip (Xuyên tường): OFF")
noclipButton.MouseButton1Click:Connect(function()
    state.noclip = not state.noclip
    noclipButton.Text = "👻 Noclip (Xuyên tường): " .. (state.noclip and "ON" or "OFF")
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

local fixLagButton = button("🧹 Fix Lag / Low Graphics: OFF")
local effectsDisabled = false
fixLagButton.MouseButton1Click:Connect(function()
    effectsDisabled = not effectsDisabled
    fixLagButton.Text = "🧹 Fix Lag: " .. (effectsDisabled and "ON" or "OFF")
    for _, obj in ipairs(Workspace:GetDescendants()) do
        if obj:IsA("ParticleEmitter") or obj:IsA("Trail") or obj:IsA("Beam") then
            obj.Enabled = not effectsDisabled
        end
    end
    local statusText = "Effects restored"
    if effectsDisabled then
        statusText = "Effects optimized"
    end
    setStatus(statusText)
end)

local frames = 0
local last = os.clock()
connections.fps = RunService.RenderStepped:Connect(function()
    frames += 1
    local now = os.clock()
    if now - last >= 1 then
        fpsLabel.Text = "FPS: " .. frames
        frames = 0
        last = now
    end
end)
local function setOpen(value)
    state.open = value
    main.Visible = value
end

toggle.MouseButton1Click:Connect(function()
    setOpen(not state.open)
end)

close.MouseButton1Click:Connect(function()
    setOpen(false)
end)

local dragging = false
local dragStart, startPos

header.InputBegan:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseButton1 then
        dragging = true
        dragStart = input.Position
        startPos = main.Position
    end
end)

UserInputService.InputEnded:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseButton1 then
        dragging = false
    end
end)

UserInputService.InputChanged:Connect(function(input)
    if dragging and input.UserInputType == Enum.UserInputType.MouseMovement then
        local delta = input.Position - dragStart
        main.Position = UDim2.new(
            startPos.X.Scale,
            startPos.X.Offset + delta.X,
            startPos.Y.Scale,
            startPos.Y.Offset + delta.Y
        )
    end
end)

setStatus("VanhHub God Edition loaded successfully!")	


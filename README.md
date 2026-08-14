local player = game.Players.LocalPlayer
local uis = game:GetService("UserInputService")
local run = game:GetService("RunService")

local flying = false
local speedOn = false
local jumpOn = false

local flySpeed = 60
local walkSpeed = 16
local jumpPower = 50

local connection

local gui = Instance.new("ScreenGui")
gui.Name = "Vanh"
gui.ResetOnSpawn = false
gui.Parent = player:WaitForChild("PlayerGui")

local open = Instance.new("TextButton")
open.Size = UDim2.new(0,85,0,42)
open.Position = UDim2.new(0,20,0.5,-21)
open.BackgroundColor3 = Color3.fromRGB(25,25,30)
open.Text = "VANH"
open.TextColor3 = Color3.fromRGB(255,255,255)
open.TextSize = 18
open.Font = Enum.Font.GothamBold
open.Parent = gui

local openCorner = Instance.new("UICorner")
openCorner.CornerRadius = UDim.new(0,10)
openCorner.Parent = open

local frame = Instance.new("Frame")
frame.Size = UDim2.new(0,330,0,390)
frame.Position = UDim2.new(0.5,-165,0.5,-165)
frame.BackgroundColor3 = Color3.fromRGB(20,20,25)
frame.Visible = false
frame.Parent = gui

local frameCorner = Instance.new("UICorner")
frameCorner.CornerRadius = UDim.new(0,15)
frameCorner.Parent = frame

local top = Instance.new("Frame")
top.Size = UDim2.new(1,0,0,55)
top.BackgroundColor3 = Color3.fromRGB(28,28,34)
top.Parent = frame

local topCorner = Instance.new("UICorner")
topCorner.CornerRadius = UDim.new(0,15)
topCorner.Parent = top

local title = Instance.new("TextLabel")
title.Size = UDim2.new(1,-100,1,0)
title.Position = UDim2.new(0,18,0,0)
title.BackgroundTransparency = 1
title.Text = "VANH"
title.TextColor3 = Color3.fromRGB(255,255,255)
title.TextSize = 22
title.Font = Enum.Font.GothamBold
title.TextXAlignment = Enum.TextXAlignment.Left
title.Parent = top

local close = Instance.new("TextButton")
close.Size = UDim2.new(0,35,0,35)
close.Position = UDim2.new(1,-45,0,10)
close.BackgroundColor3 = Color3.fromRGB(180,55,55)
close.Text = "X"
close.TextColor3 = Color3.fromRGB(255,255,255)
close.TextSize = 16
close.Font = Enum.Font.GothamBold
close.Parent = top

local closeCorner = Instance.new("UICorner")
closeCorner.CornerRadius = UDim.new(0,9)
closeCorner.Parent = close

local settings = Instance.new("TextButton")
settings.Size = UDim2.new(0,140,0,38)
settings.Position = UDim2.new(0,20,0,70)
settings.BackgroundColor3 = Color3.fromRGB(55,55,65)
settings.Text = "SETTINGS"
settings.TextColor3 = Color3.fromRGB(255,255,255)
settings.TextSize = 14
settings.Font = Enum.Font.GothamBold
settings.Parent = frame

local settingsCorner = Instance.new("UICorner")
settingsCorner.CornerRadius = UDim.new(0,9)
settingsCorner.Parent = settings

local farm = Instance.new("TextButton")
farm.Size = UDim2.new(0,140,0,38)
farm.Position = UDim2.new(0,170,0,70)
farm.BackgroundColor3 = Color3.fromRGB(35,35,42)
farm.Text = "FARM"
farm.TextColor3 = Color3.fromRGB(180,180,190)
farm.TextSize = 14
farm.Font = Enum.Font.GothamBold
farm.Parent = frame

local farmCorner = Instance.new("UICorner")
farmCorner.CornerRadius = UDim.new(0,9)
farmCorner.Parent = farm

local settingsPage = Instance.new("Frame")
settingsPage.Size = UDim2.new(1,-40,1,-125)
settingsPage.Position = UDim2.new(0,20,0,120)
settingsPage.BackgroundTransparency = 1
settingsPage.Parent = frame

local farmPage = Instance.new("Frame")
farmPage.Size = UDim2.new(1,-40,1,-125)
farmPage.Position = UDim2.new(0,20,0,120)
farmPage.BackgroundTransparency = 1
farmPage.Visible = false
farmPage.Parent = frame

local function createButton(text,y,color)
	local button = Instance.new("TextButton")
	button.Size = UDim2.new(0,100,0,38)
	button.Position = UDim2.new(0,0,0,y)
	button.BackgroundColor3 = color
	button.Text = text
	button.TextColor3 = Color3.fromRGB(255,255,255)
	button.TextSize = 12
	button.Font = Enum.Font.GothamBold
	button.Parent = settingsPage
	local corner = Instance.new("UICorner")
	corner.CornerRadius = UDim.new(0,9)
	corner.Parent = button
	return button
end

local function createBox(value,y)
	local box = Instance.new("TextBox")
	box.Size = UDim2.new(0,165,0,38)
	box.Position = UDim2.new(0,125,0,y)
	box.BackgroundColor3 = Color3.fromRGB(35,35,42)
	box.Text = tostring(value)
	box.TextColor3 = Color3.fromRGB(255,255,255)
	box.TextSize = 14
	box.Font = Enum.Font.Gotham
	box.ClearTextOnFocus = false
	box.Parent = settingsPage
	local corner = Instance.new("UICorner")
	corner.CornerRadius = UDim.new(0,9)
	corner.Parent = box
	return box
end

local flyButton = createButton("FLY OFF",0,Color3.fromRGB(90,90,100))
local flyBox = createBox(flySpeed,0)

local speedButton = createButton("SPEED OFF",55,Color3.fromRGB(90,90,100))
local speedBox = createBox(walkSpeed,55)

local jumpButton = createButton("JUMP OFF",110,Color3.fromRGB(90,90,100))
local jumpBox = createBox(jumpPower,110)

local espOn = false

local espButton = createButton("ESP OFF",165,Color3.fromRGB(90,90,100))

local espText = Instance.new("TextLabel")
espText.Size = UDim2.new(0,100,0,20)
espText.Position = UDim2.new(0,0,0,200)
espText.BackgroundTransparency = 1
espText.Text = "ESP Player"
espText.TextColor3 = Color3.fromRGB(150,150,160)
espText.TextSize = 11
espText.Font = Enum.Font.Gotham
espText.Parent = settingsPage

local espFolder = Instance.new("Folder")
espFolder.Name = "VanhESP"
espFolder.Parent = gui

local function clearESP()
	for _,v in ipairs(espFolder:GetChildren()) do
		v:Destroy()
	end
end

local function updateESP()
	clearESP()
	if not espOn then
		return
	end
	for _,target in ipairs(game.Players:GetPlayers()) do
		if target ~= player and target.Character then
			local char = target.Character
			local humanoid = char:FindFirstChildOfClass("Humanoid")
			local head = char:FindFirstChild("Head")
			if humanoid then
				local highlight = Instance.new("Highlight")
				highlight.Name = target.Name .. "_Highlight"
				highlight.Adornee = char
				highlight.FillTransparency = 0.65
				highlight.OutlineTransparency = 0
				highlight.Parent = espFolder
				if head then
					local billboard = Instance.new("BillboardGui")
					billboard.Name = target.Name .. "_Info"
					billboard.Adornee = head
					billboard.Size = UDim2.new(0,240,0,55)
					billboard.StudsOffset = Vector3.new(0,3,0)
					billboard.AlwaysOnTop = true
					billboard.Parent = espFolder
					local info = Instance.new("TextLabel")
					info.Size = UDim2.new(1,0,1,0)
					info.BackgroundTransparency = 1
					info.TextColor3 = Color3.fromRGB(255,255,255)
					info.TextStrokeTransparency = 0
					info.TextSize = 14
					info.Font = Enum.Font.GothamBold
					info.Text = target.DisplayName .. " [" .. target.Name .. "]\nHP: " ..
						math.floor(humanoid.Health) .. "/" .. math.floor(humanoid.MaxHealth)
					info.Parent = billboard
				end
			end
		end
	end
end

espButton.MouseButton1Click:Connect(function()
	espOn = not espOn
	if espOn then
		espButton.Text = "ESP ON"
		espButton.BackgroundColor3 = Color3.fromRGB(45,170,90)
		updateESP()
	else
		espButton.Text = "ESP OFF"
		espButton.BackgroundColor3 = Color3.fromRGB(90,90,100)
		clearESP()
	end
end)

game.Players.PlayerAdded:Connect(function(target)
	target.CharacterAdded:Connect(function()
		if espOn then
			task.wait(0.3)
			updateESP()
		end
	end)
end)

game.Players.PlayerRemoving:Connect(function()
	if espOn then
		updateESP()
	end
end)

task.spawn(function()
	while gui.Parent do
		if espOn then
			updateESP()
		end
		task.wait(0.25)
	end
end)

local flyText = Instance.new("TextLabel")
flyText.Size = UDim2.new(0,100,0,20)
flyText.Position = UDim2.new(0,0,0,35)
flyText.BackgroundTransparency = 1
flyText.Text = "Fly Speed"
flyText.TextColor3 = Color3.fromRGB(150,150,160)
flyText.TextSize = 11
flyText.Font = Enum.Font.Gotham
flyText.Parent = settingsPage

local speedText = Instance.new("TextLabel")
speedText.Size = UDim2.new(0,100,0,20)
speedText.Position = UDim2.new(0,0,0,90)
speedText.BackgroundTransparency = 1
speedText.Text = "Walk Speed"
speedText.TextColor3 = Color3.fromRGB(150,150,160)
speedText.TextSize = 11
speedText.Font = Enum.Font.Gotham
speedText.Parent = settingsPage

local jumpText = Instance.new("TextLabel")
jumpText.Size = UDim2.new(0,100,0,20)
jumpText.Position = UDim2.new(0,0,0,145)
jumpText.BackgroundTransparency = 1
jumpText.Text = "Jump Power"
jumpText.TextColor3 = Color3.fromRGB(150,150,160)
jumpText.TextSize = 11
jumpText.Font = Enum.Font.Gotham
jumpText.Parent = settingsPage

local farmText = Instance.new("TextLabel")
farmText.Size = UDim2.new(1,0,0,40)
farmText.Position = UDim2.new(0,0,0,20)
farmText.BackgroundTransparency = 1
farmText.Text = "FARM"
farmText.TextColor3 = Color3.fromRGB(150,150,160)
farmText.TextSize = 16
farmText.Font = Enum.Font.GothamBold
farmText.Parent = farmPage

open.MouseButton1Click:Connect(function()
	frame.Visible = not frame.Visible
end)

settings.MouseButton1Click:Connect(function()
	settingsPage.Visible = true
	farmPage.Visible = false
	settings.BackgroundColor3 = Color3.fromRGB(55,55,65)
	farm.BackgroundColor3 = Color3.fromRGB(35,35,42)
end)

farm.MouseButton1Click:Connect(function()
	settingsPage.Visible = false
	farmPage.Visible = true
	settings.BackgroundColor3 = Color3.fromRGB(35,35,42)
	farm.BackgroundColor3 = Color3.fromRGB(55,55,65)
end)

flyButton.MouseButton1Click:Connect(function()
	flying = not flying
	if flying then
		flyButton.Text = "FLY ON"
		flyButton.BackgroundColor3 = Color3.fromRGB(45,170,90)
	else
		flyButton.Text = "FLY OFF"
		flyButton.BackgroundColor3 = Color3.fromRGB(90,90,100)
		local char = player.Character
		local hrp = char and char:FindFirstChild("HumanoidRootPart")
		if hrp then
			local bv = hrp:FindFirstChild("FlyVelocity")
			if bv then
				bv:Destroy()
			end
		end
	end
end)

speedButton.MouseButton1Click:Connect(function()
	speedOn = not speedOn
	local char = player.Character
	local humanoid = char and char:FindFirstChildOfClass("Humanoid")
	if speedOn then
		speedButton.Text = "SPEED ON"
		speedButton.BackgroundColor3 = Color3.fromRGB(45,170,90)
		if humanoid then
			humanoid.WalkSpeed = walkSpeed
		end
	else
		speedButton.Text = "SPEED OFF"
		speedButton.BackgroundColor3 = Color3.fromRGB(90,90,100)
		if humanoid then
			humanoid.WalkSpeed = 16
		end
	end
end)

jumpButton.MouseButton1Click:Connect(function()
	jumpOn = not jumpOn
	local char = player.Character
	local humanoid = char and char:FindFirstChildOfClass("Humanoid")
	if jumpOn then
		jumpButton.Text = "JUMP ON"
		jumpButton.BackgroundColor3 = Color3.fromRGB(45,170,90)
		if humanoid then
			humanoid.UseJumpPower = true
			humanoid.JumpPower = jumpPower
		end
	else
		jumpButton.Text = "JUMP OFF"
		jumpButton.BackgroundColor3 = Color3.fromRGB(90,90,100)
		if humanoid then
			humanoid.UseJumpPower = true
			humanoid.JumpPower = 50
		end
	end
end)

flyBox.FocusLost:Connect(function()
	local value = tonumber(flyBox.Text)
	if value and value >= 1 then
		flySpeed = value
	else
		flyBox.Text = tostring(flySpeed)
	end
end)

speedBox.FocusLost:Connect(function()
	local value = tonumber(speedBox.Text)
	if value and value >= 0 then
		walkSpeed = value
		if speedOn then
			local char = player.Character
			local humanoid = char and char:FindFirstChildOfClass("Humanoid")
			if humanoid then
				humanoid.WalkSpeed = walkSpeed
			end
		end
	else
		speedBox.Text = tostring(walkSpeed)
	end
end)

jumpBox.FocusLost:Connect(function()
	local value = tonumber(jumpBox.Text)
	if value and value >= 0 then
		jumpPower = value
		if jumpOn then
			local char = player.Character
			local humanoid = char and char:FindFirstChildOfClass("Humanoid")
			if humanoid then
				humanoid.UseJumpPower = true
				humanoid.JumpPower = jumpPower
			end
		end
	else
		jumpBox.Text = tostring(jumpPower)
	end
end)

local function setupCharacter(char)
	local humanoid = char:WaitForChild("Humanoid")
	if speedOn then
		humanoid.WalkSpeed = walkSpeed
	end
	if jumpOn then
		humanoid.UseJumpPower = true
		humanoid.JumpPower = jumpPower
	end
end

if player.Character then
	setupCharacter(player.Character)
end

player.CharacterAdded:Connect(function(char)
	setupCharacter(char)
	if espOn then
		task.wait(0.2)
		updateESP()
	end
end)

connection = run.RenderStepped:Connect(function()
	if not flying then
		return
	end
	local char = player.Character
	local hrp = char and char:FindFirstChild("HumanoidRootPart")
	if not hrp then
		return
	end
	local bv = hrp:FindFirstChild("FlyVelocity")
	if not bv then
		bv = Instance.new("BodyVelocity")
		bv.Name = "FlyVelocity"
		bv.MaxForce = Vector3.new(math.huge,math.huge,math.huge)
		bv.Parent = hrp
	end
	local cam = workspace.CurrentCamera
	local move = Vector3.zero
	if uis:IsKeyDown(Enum.KeyCode.W) then
		move += cam.CFrame.LookVector
	end
	if uis:IsKeyDown(Enum.KeyCode.S) then
		move -= cam.CFrame.LookVector
	end
	if uis:IsKeyDown(Enum.KeyCode.A) then
		move -= cam.CFrame.RightVector
	end
	if uis:IsKeyDown(Enum.KeyCode.D) then
		move += cam.CFrame.RightVector
	end
	if uis:IsKeyDown(Enum.KeyCode.Space) then
		move += Vector3.new(0,1,0)
	end
	if uis:IsKeyDown(Enum.KeyCode.LeftControl) then
		move -= Vector3.new(0,1,0)
	end
	if move.Magnitude > 0 then
		bv.Velocity = move.Unit * flySpeed
	else
		bv.Velocity = Vector3.zero
	end
end)

local confirm = Instance.new("Frame")
confirm.Size = UDim2.new(0,280,0,145)
confirm.Position = UDim2.new(0.5,-140,0.5,-72)
confirm.BackgroundColor3 = Color3.fromRGB(25,25,30)
confirm.Visible = false
confirm.ZIndex = 10
confirm.Parent = gui

local confirmCorner = Instance.new("UICorner")
confirmCorner.CornerRadius = UDim.new(0,13)
confirmCorner.Parent = confirm

local question = Instance.new("TextLabel")
question.Size = UDim2.new(1,-20,0,65)
question.Position = UDim2.new(0,10,0,10)
question.BackgroundTransparency = 1
question.Text = "Bạn có xác định tắt script?"
question.TextColor3 = Color3.fromRGB(255,255,255)
question.TextSize = 16
question.Font = Enum.Font.GothamBold
question.TextWrapped = true
question.ZIndex = 11
question.Parent = confirm

local yes = Instance.new("TextButton")
yes.Size = UDim2.new(0,105,0,38)
yes.Position = UDim2.new(0,25,0,90)
yes.BackgroundColor3 = Color3.fromRGB(180,55,55)
yes.Text = "CÓ"
yes.TextColor3 = Color3.fromRGB(255,255,255)
yes.TextSize = 14
yes.Font = Enum.Font.GothamBold
yes.ZIndex = 11
yes.Parent = confirm

local yesCorner = Instance.new("UICorner")
yesCorner.CornerRadius = UDim.new(0,8)
yesCorner.Parent = yes

local no = Instance.new("TextButton")
no.Size = UDim2.new(0,105,0,38)
no.Position = UDim2.new(0,150,0,90)
no.BackgroundColor3 = Color3.fromRGB(60,60,70)
no.Text = "KHÔNG"
no.TextColor3 = Color3.fromRGB(255,255,255)
no.TextSize = 14
no.Font = Enum.Font.GothamBold
no.ZIndex = 11
no.Parent = confirm

local noCorner = Instance.new("UICorner")
noCorner.CornerRadius = UDim.new(0,8)
noCorner.Parent = no

close.MouseButton1Click:Connect(function()
	confirm.Visible = true
end)

no.MouseButton1Click:Connect(function()
	confirm.Visible = false
end)

yes.MouseButton1Click:Connect(function()
	flying = false
	espOn = false
	clearESP()
	local char = player.Character
	local hrp = char and char:FindFirstChild("HumanoidRootPart")
	if hrp then
		local bv = hrp:FindFirstChild("FlyVelocity")
		if bv then
			bv:Destroy()
		end
	end
	if connection then
		connection:Disconnect()
		connection = nil
	end
	gui:Destroy()
end)

local dragging = false
local dragStart
local startPos

top.InputBegan:Connect(function(input)
	if input.UserInputType == Enum.UserInputType.MouseButton1 then
		dragging = true
		dragStart = input.Position
		startPos = frame.Position
	end
end)

top.InputEnded:Connect(function(input)
	if input.UserInputType == Enum.UserInputType.MouseButton1 then
		dragging = false
	end
end)

uis.InputChanged:Connect(function(input)
	if dragging and input.UserInputType == Enum.UserInputType.MouseMovement then
		local delta = input.Position - dragStart
		frame.Position = UDim2.new(
			startPos.X.Scale,
			startPos.X.Offset + delta.X,
			startPos.Y.Scale,
			startPos.Y.Offset + delta.Y
		)
	end
end)

local openDragging = false
local openDragStart
local openStartPos

open.InputBegan:Connect(function(input)
	if input.UserInputType == Enum.UserInputType.MouseButton1 then
		openDragging = true
		openDragStart = input.Position
		openStartPos = open.Position
	end
end)

open.InputEnded:Connect(function(input)
	if input.UserInputType == Enum.UserInputType.MouseButton1 then
		openDragging = false
	end
end)

uis.InputChanged:Connect(function(input)
	if openDragging and input.UserInputType == Enum.UserInputType.MouseMovement then
		local delta = input.Position - openDragStart
		open.Position = UDim2.new(
			openStartPos.X.Scale,
			openStartPos.X.Offset + delta.X,
			openStartPos.Y.Scale,
			openStartPos.Y.Offset + delta.Y
		)
	end
end)

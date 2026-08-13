local player = game.Players.LocalPlayer
local uis = game:GetService("UserInputService")
local run = game:GetService("RunService")

local flying = false
local speed = 60
local connection

local gui = Instance.new("ScreenGui")
gui.Name = "Vanh"
gui.ResetOnSpawn = false
gui.Parent = player:WaitForChild("PlayerGui")

local open = Instance.new("TextButton")
open.Size = UDim2.new(0,85,0,42)
open.Position = UDim2.new(0,20,0.5,-21)
open.BackgroundColor3 = Color3.fromRGB(30,30,35)
open.Text = "VANH"
open.TextColor3 = Color3.fromRGB(255,255,255)
open.TextSize = 18
open.Font = Enum.Font.GothamBold
open.Parent = gui

local openCorner = Instance.new("UICorner")
openCorner.CornerRadius = UDim.new(0,10)
openCorner.Parent = open

local frame = Instance.new("Frame")
frame.Size = UDim2.new(0,270,0,210)
frame.Position = UDim2.new(0.5,-135,0.5,-105)
frame.BackgroundColor3 = Color3.fromRGB(22,22,27)
frame.Visible = false
frame.Parent = gui

local frameCorner = Instance.new("UICorner")
frameCorner.CornerRadius = UDim.new(0,14)
frameCorner.Parent = frame

local title = Instance.new("TextLabel")
title.Size = UDim2.new(1,-60,0,40)
title.Position = UDim2.new(0,10,0,5)
title.BackgroundTransparency = 1
title.Text = "VANH"
title.TextColor3 = Color3.fromRGB(255,255,255)
title.TextSize = 24
title.Font = Enum.Font.GothamBold
title.Parent = frame

local close = Instance.new("TextButton")
close.Size = UDim2.new(0,32,0,32)
close.Position = UDim2.new(1,-42,0,8)
close.BackgroundColor3 = Color3.fromRGB(180,55,55)
close.Text = "X"
close.TextColor3 = Color3.fromRGB(255,255,255)
close.TextSize = 16
close.Font = Enum.Font.GothamBold
close.Parent = frame

local closeCorner = Instance.new("UICorner")
closeCorner.CornerRadius = UDim.new(0,8)
closeCorner.Parent = close

local status = Instance.new("TextLabel")
status.Size = UDim2.new(1,-20,0,25)
status.Position = UDim2.new(0,10,0,42)
status.BackgroundTransparency = 1
status.Text = "Status: OFF"
status.TextColor3 = Color3.fromRGB(255,80,80)
status.TextSize = 14
status.Font = Enum.Font.Gotham
status.Parent = frame

local on = Instance.new("TextButton")
on.Size = UDim2.new(0,105,0,40)
on.Position = UDim2.new(0,20,0,75)
on.BackgroundColor3 = Color3.fromRGB(45,170,90)
on.Text = "ON"
on.TextColor3 = Color3.fromRGB(255,255,255)
on.TextSize = 16
on.Font = Enum.Font.GothamBold
on.Parent = frame

local onCorner = Instance.new("UICorner")
onCorner.CornerRadius = UDim.new(0,9)
onCorner.Parent = on

local off = Instance.new("TextButton")
off.Size = UDim2.new(0,105,0,40)
off.Position = UDim2.new(0,145,0,75)
off.BackgroundColor3 = Color3.fromRGB(180,55,55)
off.Text = "OFF"
off.TextColor3 = Color3.fromRGB(255,255,255)
off.TextSize = 16
off.Font = Enum.Font.GothamBold
off.Parent = frame

local offCorner = Instance.new("UICorner")
offCorner.CornerRadius = UDim.new(0,9)
offCorner.Parent = off

local speedLabel = Instance.new("TextLabel")
speedLabel.Size = UDim2.new(0,80,0,30)
speedLabel.Position = UDim2.new(0,20,0,125)
speedLabel.BackgroundTransparency = 1
speedLabel.Text = "Speed:"
speedLabel.TextColor3 = Color3.fromRGB(220,220,220)
speedLabel.TextSize = 15
speedLabel.Font = Enum.Font.Gotham
speedLabel.TextXAlignment = Enum.TextXAlignment.Left
speedLabel.Parent = frame

local speedBox = Instance.new("TextBox")
speedBox.Size = UDim2.new(0,150,0,32)
speedBox.Position = UDim2.new(0,100,0,124)
speedBox.BackgroundColor3 = Color3.fromRGB(35,35,42)
speedBox.Text = tostring(speed)
speedBox.PlaceholderText = "Speed"
speedBox.TextColor3 = Color3.fromRGB(255,255,255)
speedBox.TextSize = 14
speedBox.Font = Enum.Font.Gotham
speedBox.ClearTextOnFocus = false
speedBox.Parent = frame

local speedCorner = Instance.new("UICorner")
speedCorner.CornerRadius = UDim.new(0,8)
speedCorner.Parent = speedBox

local info = Instance.new("TextLabel")
info.Size = UDim2.new(1,-20,0,30)
info.Position = UDim2.new(0,10,0,165)
info.BackgroundTransparency = 1
info.Text = "WASD • SPACE ↑ • CTRL ↓"
info.TextColor3 = Color3.fromRGB(140,140,150)
info.TextSize = 12
info.Font = Enum.Font.Gotham
info.Parent = frame

open.MouseButton1Click:Connect(function()
	frame.Visible = not frame.Visible
end)

on.MouseButton1Click:Connect(function()
	flying = true
	status.Text = "Status: ON"
	status.TextColor3 = Color3.fromRGB(70,220,110)
end)

off.MouseButton1Click:Connect(function()
	flying = false
	status.Text = "Status: OFF"
	status.TextColor3 = Color3.fromRGB(255,80,80)
	local char = player.Character
	local hrp = char and char:FindFirstChild("HumanoidRootPart")
	if hrp then
		local bv = hrp:FindFirstChild("FlyVelocity")
		if bv then
			bv:Destroy()
		end
	end
end)

speedBox.FocusLost:Connect(function()
	local value = tonumber(speedBox.Text)
	if value and value >= 1 then
		speed = value
	else
		speedBox.Text = tostring(speed)
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
		bv.Velocity = move.Unit * speed
	else
		bv.Velocity = Vector3.zero
	end
end)

close.MouseButton1Click:Connect(function()
	flying = false
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
	end
	gui:Destroy()
end)

local dragging = false
local dragStart
local startPos

open.InputBegan:Connect(function(input)
	if input.UserInputType == Enum.UserInputType.MouseButton1 then
		dragging = true
		dragStart = input.Position
		startPos = open.Position
	end
end)

open.InputEnded:Connect(function(input)
	if input.UserInputType == Enum.UserInputType.MouseButton1 then
		dragging = false
	end
end)

uis.InputChanged:Connect(function(input)
	if dragging and input.UserInputType == Enum.UserInputType.MouseMovement then
		local delta = input.Position - dragStart
		open.Position = UDim2.new(
			startPos.X.Scale,
			startPos.X.Offset + delta.X,
			startPos.Y.Scale,
			startPos.Y.Offset + delta.Y
		)
	end
end)

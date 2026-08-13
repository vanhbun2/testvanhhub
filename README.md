local player = game.Players.LocalPlayer
local uis = game:GetService("UserInputService")
local run = game:GetService("RunService")

local flying = false
local speed = 60

local gui = Instance.new("ScreenGui")
gui.Name = "FlyGui"
gui.ResetOnSpawn = false
gui.Parent = player:WaitForChild("PlayerGui")
local open = Instance.new("TextButton")
open.Size = UDim2.new(0, 80, 0, 40)
open.Position = UDim2.new(0, 20, 0.5, -20)
open.Text = "FLY"
open.Parent = gui
local frame = Instance.new("Frame")
frame.Size = UDim2.new(0, 220, 0, 130)
frame.Position = UDim2.new(0.5, -110, 0.5, -65)
frame.Visible = false
frame.Parent = gui
local title = Instance.new("TextLabel")
title.Size = UDim2.new(1, 0, 0, 35)
title.Text = "FLY CONTROL"
title.Parent = frame
local on = Instance.new("TextButton")
on.Size = UDim2.new(0, 80, 0, 40)
on.Position = UDim2.new(0, 20, 0, 55)
on.Text = "ON"
on.Parent = frame
local off = Instance.new("TextButton")
off.Size = UDim2.new(0, 80, 0, 40)
off.Position = UDim2.new(0, 120, 0, 55)
off.Text = "OFF"
off.Parent = frame

open.MouseButton1Click:Connect(function()
	frame.Visible = not frame.Visible
end)

on.MouseButton1Click:Connect(function()
	flying = true
end)

off.MouseButton1Click:Connect(function()
	flying = false
	local char = player.Character
	local hrp = char and char:FindFirstChild("HumanoidRootPart")
	if hrp then
		local bv = hrp:FindFirstChild("FlyVelocity")
		if bv then
			bv:Destroy()
		end
	end
end)
run.RenderStepped:Connect(function()
	if not flying then return end
	local char = player.Character
	local hrp = char and char:FindFirstChild("HumanoidRootPart")
	if not hrp then return end
	local bv = hrp:FindFirstChild("FlyVelocity")
	if not bv then
		bv = Instance.new("BodyVelocity")
		bv.Name = "FlyVelocity"
		bv.MaxForce = Vector3.new(math.huge, math.huge, math.huge)
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
		move += Vector3.new(0, 1, 0)
	end
	if uis:IsKeyDown(Enum.KeyCode.LeftControl) then
		move -= Vector3.new(0, 1, 0)
	end
	if move.Magnitude > 0 then
		bv.Velocity = move.Unit * speed
	else
		bv.Velocity = Vector3.zero
	end
end)

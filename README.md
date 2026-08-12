local player = game.Players.LocalPlayer
local mouse = player:GetMouse()
local UserInputService = game:GetService("UserInputService")
local RunService = game:GetService("RunService")

local screenGui = script.Parent
local openBtn = screenGui:WaitForChild("OpenButton")
local mainFrame = screenGui:WaitForChild("MainFrame")

local flyBtn = mainFrame:WaitForChild("FlyBtn")
local speedBtn = mainFrame:WaitForChild("SpeedBtn")
local jumpBtn = mainFrame:WaitForChild("JumpBtn")
local espBtn = mainFrame:WaitForChild("ESPBtn")

mainFrame.Visible = false
local speedEnabled = false
local jumpEnabled = false
local flyEnabled = false
local espEnabled = false

openBtn.MouseButton1Click:Connect(function()
	mainFrame.Visible = not mainFrame.Visible
end)

speedBtn.MouseButton1Click:Connect(function()
	speedEnabled = not speedEnabled
	local char = player.Character
	if char and char:FindFirstChild("Humanoid") then
		if speedEnabled then
			char.Humanoid.WalkSpeed = 100
			speedBtn.BackgroundColor3 = Color3.fromRGB(0, 170, 0)
		else
			char.Humanoid.WalkSpeed = 16
			speedBtn.BackgroundColor3 = Color3.fromRGB(50, 50, 50)
		end
	end
end)

jumpBtn.MouseButton1Click:Connect(function()
	jumpEnabled = not jumpEnabled
	local char = player.Character
	if char and char:FindFirstChild("Humanoid") then
		char.Humanoid.UseJumpPower = true
		if jumpEnabled then
			char.Humanoid.JumpPower = 150
			jumpBtn.BackgroundColor3 = Color3.fromRGB(0, 170, 0)
		else
			char.Humanoid.JumpPower = 50
			jumpBtn.BackgroundColor3 = Color3.fromRGB(50, 50, 50)
		end
	end
end)

local function createESP(targetPlayer)
	if targetPlayer.Character and not targetPlayer.Character:FindFirstChild("ESPHighlight") then
		local highlight = Instance.new("Highlight")
		highlight.Name = "ESPHighlight"
		highlight.Parent = targetPlayer.Character
		highlight.FillColor = Color3.fromRGB(255, 0, 0)
		highlight.OutlineColor = Color3.fromRGB(255, 255, 255)
	end
end

local function removeESP()
	for _, p in pairs(game.Players:GetPlayers()) do
		if p.Character and p.Character:FindFirstChild("ESPHighlight") then
			p.Character.ESPHighlight:Destroy()
		end
	end
end

espBtn.MouseButton1Click:Connect(function()
	espEnabled = not espEnabled
	if espEnabled then
		espBtn.BackgroundColor3 = Color3.fromRGB(0, 170, 0)
	else
		espBtn.BackgroundColor3 = Color3.fromRGB(50, 50, 50)
		removeESP()
	end
end)

RunService.RenderStepped:Connect(function()
	if espEnabled then
		for _, p in pairs(game.Players:GetPlayers()) do
			if p ~= player and p.Character then
				createESP(p)
			end
		end
	end
end)

local bv, bg

flyBtn.MouseButton1Click:Connect(function()
	flyEnabled = not flyEnabled
	local char = player.Character
	local root = char and char:FindFirstChild("HumanoidRootPart")
	local hum = char and char:FindFirstChild("Humanoid")
	if flyEnabled then
		flyBtn.BackgroundColor3 = Color3.fromRGB(0, 170, 0)
		if root and hum then
			hum.PlatformStand = true
			bv = Instance.new("BodyVelocity", root)
			bv.MaxForce = Vector3.new(math.huge, math.huge, math.huge)
			bv.Velocity = Vector3.new(0, 0, 0)
			bg = Instance.new("BodyGyro", root)
			bg.MaxTorque = Vector3.new(math.huge, math.huge, math.huge)
			bg.CFrame = root.CFrame
		end
	else
		flyBtn.BackgroundColor3 = Color3.fromRGB(50, 50, 50)
		if bv then
			bv:Destroy()
		end
		if bg then
			bg:Destroy()
		end
		if hum then
			hum.PlatformStand = false
		end
	end
end)

RunService.RenderStepped:Connect(function()
	if flyEnabled and bv and bg then
		local camera = workspace.CurrentCamera
		local moveDir = Vector3.new(0, 0, 0)
		if UserInputService:IsKeyDown(Enum.KeyCode.W) then
			moveDir = moveDir + camera.CFrame.LookVector
		end
		if UserInputService:IsKeyDown(Enum.KeyCode.S) then
			moveDir = moveDir - camera.CFrame.LookVector
		end
		if UserInputService:IsKeyDown(Enum.KeyCode.A) then
			moveDir = moveDir - camera.CFrame.RightVector
		end
		if UserInputService:IsKeyDown(Enum.KeyCode.D) then
			moveDir = moveDir + camera.CFrame.RightVector
		end
		if UserInputService:IsKeyDown(Enum.KeyCode.Space) then
			moveDir = moveDir + Vector3.new(0, 1, 0)
		end
		if UserInputService:IsKeyDown(Enum.KeyCode.LeftShift) then
			moveDir = moveDir - Vector3.new(0, 1, 0)
		end
		bv.Velocity = moveDir * 50
		bg.CFrame = camera.CFrame
	end
end)

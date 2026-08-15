local player=game.Players.LocalPlayer
local uis=game:GetService("UserInputService")
local run=game:GetService("RunService")
local players=game:GetService("Players")

local flying=false
local speedOn=false
local jumpOn=false
local espOn=false
local aimOn=false

local flySpeed=60
local walkSpeed=60
local jumpPower=50

-- AIM SETTINGS
local MAX_RANGE=120
local currentTarget=nil
local currentDistance=math.huge

local connection

local gui=Instance.new("ScreenGui")
gui.Name="Vanh"
gui.ResetOnSpawn=false
gui.Parent=player:WaitForChild("PlayerGui")

local open=Instance.new("TextButton")
open.Size=UDim2.new(0,85,0,42)
open.Position=UDim2.new(0,20,0.5,-21)
open.BackgroundColor3=Color3.fromRGB(25,25,30)
open.Text="VANH"
open.TextColor3=Color3.fromRGB(255,255,255)
open.TextSize=18
open.Font=Enum.Font.GothamBold
open.Parent=gui

local openCorner=Instance.new("UICorner")
openCorner.CornerRadius=UDim.new(0,10)
openCorner.Parent=open

local frame=Instance.new("Frame")
frame.Size=UDim2.new(0,330,0,410)
frame.Position=UDim2.new(0.5,-165,0.5,-205)
frame.BackgroundColor3=Color3.fromRGB(20,20,25)
frame.Visible=false
frame.Parent=gui

local frameCorner=Instance.new("UICorner")
frameCorner.CornerRadius=UDim.new(0,15)
frameCorner.Parent=frame

local top=Instance.new("Frame")
top.Size=UDim2.new(1,0,0,55)
top.BackgroundColor3=Color3.fromRGB(28,28,34)
top.Parent=frame

local topCorner=Instance.new("UICorner")
topCorner.CornerRadius=UDim.new(0,15)
topCorner.Parent=top

local title=Instance.new("TextLabel")
title.Size=UDim2.new(1,-100,1,0)
title.Position=UDim2.new(0,18,0,0)
title.BackgroundTransparency=1
title.Text="VANH"
title.TextColor3=Color3.fromRGB(255,255,255)
title.TextSize=22
title.Font=Enum.Font.GothamBold
title.TextXAlignment=Enum.TextXAlignment.Left
title.Parent=top

local close=Instance.new("TextButton")
close.Size=UDim2.new(0,35,0,35)
close.Position=UDim2.new(1,-45,0,10)
close.BackgroundColor3=Color3.fromRGB(180,55,55)
close.Text="X"
close.TextColor3=Color3.fromRGB(255,255,255)
close.TextSize=16
close.Font=Enum.Font.GothamBold
close.Parent=top

local closeCorner=Instance.new("UICorner")
closeCorner.CornerRadius=UDim.new(0,9)
closeCorner.Parent=close

local settings=Instance.new("TextButton")
settings.Size=UDim2.new(0,140,0,38)
settings.Position=UDim2.new(0,20,0,70)
settings.BackgroundColor3=Color3.fromRGB(55,55,65)
settings.Text="SETTINGS"
settings.TextColor3=Color3.fromRGB(255,255,255)
settings.TextSize=14
settings.Font=Enum.Font.GothamBold
settings.Parent=frame

local settingsCorner=Instance.new("UICorner")
settingsCorner.CornerRadius=UDim.new(0,9)
settingsCorner.Parent=settings

local aim=Instance.new("TextButton")
aim.Size=UDim2.new(0,140,0,38)
aim.Position=UDim2.new(0,170,0,70)
aim.BackgroundColor3=Color3.fromRGB(35,35,42)
aim.Text="AIM"
aim.TextColor3=Color3.fromRGB(180,180,190)
aim.TextSize=14
aim.Font=Enum.Font.GothamBold
aim.Parent=frame

local aimCorner=Instance.new("UICorner")
aimCorner.CornerRadius=UDim.new(0,9)
aimCorner.Parent=aim

local settingsPage=Instance.new("Frame")
settingsPage.Size=UDim2.new(1,-40,1,-125)
settingsPage.Position=UDim2.new(0,20,0,120)
settingsPage.BackgroundTransparency=1
settingsPage.Parent=frame

local aimPage=Instance.new("Frame")
aimPage.Size=UDim2.new(1,-40,1,-125)
aimPage.Position=UDim2.new(0,20,0,120)
aimPage.BackgroundTransparency=1
aimPage.Visible=false
aimPage.Parent=frame

local function createButton(text,y)
	local b=Instance.new("TextButton")
	b.Size=UDim2.new(0,100,0,38)
	b.Position=UDim2.new(0,0,0,y)
	b.BackgroundColor3=Color3.fromRGB(90,90,100)
	b.Text=text
	b.TextColor3=Color3.fromRGB(255,255,255)
	b.TextSize=12
	b.Font=Enum.Font.GothamBold
	b.Parent=settingsPage
	local c=Instance.new("UICorner")
	c.CornerRadius=UDim.new(0,9)
	c.Parent=b
	return b
end

local function createBox(value,y)
	local b=Instance.new("TextBox")
	b.Size=UDim2.new(0,165,0,38)
	b.Position=UDim2.new(0,125,0,y)
	b.BackgroundColor3=Color3.fromRGB(35,35,42)
	b.Text=tostring(value)
	b.TextColor3=Color3.fromRGB(255,255,255)
	b.TextSize=14
	b.Font=Enum.Font.Gotham
	b.ClearTextOnFocus=false
	b.Parent=settingsPage
	local c=Instance.new("UICorner")
	c.CornerRadius=UDim.new(0,9)
	c.Parent=b
	return b
end

local flyButton=createButton("FLY OFF",0)
local flyBox=createBox(flySpeed,0)

local speedButton=createButton("SPEED OFF",55)
local speedBox=createBox(walkSpeed,55)

local jumpButton=createButton("JUMP OFF",110)
local jumpBox=createBox(jumpPower,110)

local espButton=createButton("ESP OFF",165)

local espFolder=Instance.new("Folder")
espFolder.Name="VanhESP"
espFolder.Parent=gui

local function clearESP()
	for _,v in ipairs(espFolder:GetChildren()) do
		v:Destroy()
	end
end

local function makeESP(target)
	if not espOn or target==player then
		return
	end
	local char=target.Character
	if not char then
		return
	end
	local hum=char:FindFirstChildOfClass("Humanoid")
	local head=char:FindFirstChild("Head")
	if not hum then
		return
	end
	local h=Instance.new("Highlight")
	h.Adornee=char
	h.FillTransparency=0.65
	h.OutlineTransparency=0
	h.Parent=espFolder
	if head then
		local bb=Instance.new("BillboardGui")
		bb.Adornee=head
		bb.Size=UDim2.new(0,250,0,55)
		bb.StudsOffset=Vector3.new(0,3,0)
		bb.AlwaysOnTop=true
		bb.Parent=espFolder
		local info=Instance.new("TextLabel")
		info.Size=UDim2.new(1,0,1,0)
		info.BackgroundTransparency=1
		info.TextColor3=Color3.fromRGB(255,255,255)
		info.TextStrokeTransparency=0
		info.TextSize=14
		info.Font=Enum.Font.GothamBold
		info.Text=target.DisplayName.." ["..target.Name.."]\nHP: "..math.floor(hum.Health).."/"..math.floor(hum.MaxHealth)
		info.Parent=bb
	end
end

local function updateESP()
	clearESP()
	if not espOn then
		return
	end
	for _,p in ipairs(players:GetPlayers()) do
		makeESP(p)
	end
end

local function isEnemy(target)
	if not target then
		return false
	end
	if target==player then
		return false
	end
	if not target:IsA("Player") then
		return false
	end
	local char=target.Character
	if not char then
		return false
	end
	local hum=char:FindFirstChildOfClass("Humanoid")
	local root=char:FindFirstChild("HumanoidRootPart")
	if not hum or not root then
		return false
	end
	if hum.Health<=0 then
		return false
	end
	if player.Team~=nil and target.Team~=nil then
		if player.Team==target.Team then
			return false
		end
	end
	return true
end

local function getNearestEnemy()
	local char=player.Character
	if not char then
		return nil,math.huge
	end
	local root=char:FindFirstChild("HumanoidRootPart")
	if not root then
		return nil,math.huge
	end
	local target=nil
	local distance=MAX_RANGE
	for _,p in ipairs(players:GetPlayers()) do
		if isEnemy(p) then
			local targetChar=p.Character
			local targetRoot=targetChar:FindFirstChild("HumanoidRootPart")
			if targetRoot then
				local d=(targetRoot.Position-root.Position).Magnitude
				if d<=distance then
					distance=d
					target=p
				end
			end
		end
	end
	return target,distance
end

local function updateTarget()
	if not aimOn then
		currentTarget=nil
		currentDistance=math.huge
		return
	end
	local target,distance=getNearestEnemy()
	currentTarget=target
	currentDistance=distance
	if target then
		aimStatus.Text=
			"Status: ON\n"..
			"Target: "..target.DisplayName.."\n"..
			"Distance: "..math.floor(distance)..
			"\nRange: "..MAX_RANGE
	else
		aimStatus.Text=
			"Status: ON\n"..
			"Target: NONE\n"..
			"Range: "..MAX_RANGE
	end
end

local aimTitle=Instance.new("TextLabel")
aimTitle.Size=UDim2.new(1,0,0,30)
aimTitle.Position=UDim2.new(0,0,0,5)
aimTitle.BackgroundTransparency=1
aimTitle.Text="AIM • ENEMY PLAYER"
aimTitle.TextColor3=Color3.fromRGB(150,150,160)
aimTitle.TextSize=16
aimTitle.Font=Enum.Font.GothamBold
aimTitle.Parent=aimPage

local aimStatus=Instance.new("TextLabel")
aimStatus.Size=UDim2.new(0,290,0,100)
aimStatus.Position=UDim2.new(0,0,0,45)
aimStatus.BackgroundTransparency=1
aimStatus.Text="Status: OFF"
aimStatus.TextColor3=Color3.fromRGB(180,180,190)
aimStatus.TextSize=12
aimStatus.Font=Enum.Font.Gotham
aimStatus.TextWrapped=true
aimStatus.TextYAlignment=Enum.TextYAlignment.Top
aimStatus.Parent=aimPage

local rangeBox=Instance.new("TextBox")
rangeBox.Size=UDim2.new(0,290,0,38)
rangeBox.Position=UDim2.new(0,0,0,110)
rangeBox.BackgroundColor3=Color3.fromRGB(35,35,42)
rangeBox.Text=tostring(MAX_RANGE)
rangeBox.PlaceholderText="AIM RANGE"
rangeBox.TextColor3=Color3.fromRGB(255,255,255)
rangeBox.TextSize=14
rangeBox.Font=Enum.Font.Gotham
rangeBox.ClearTextOnFocus=false
rangeBox.Parent=aimPage

local rangeCorner=Instance.new("UICorner")
rangeCorner.CornerRadius=UDim.new(0,9)
rangeCorner.Parent=rangeBox

local aimButton=Instance.new("TextButton")
aimButton.Size=UDim2.new(0,290,0,40)
aimButton.Position=UDim2.new(0,0,0,155)
aimButton.BackgroundColor3=Color3.fromRGB(90,90,100)
aimButton.Text="AIM OFF"
aimButton.TextColor3=Color3.fromRGB(255,255,255)
aimButton.TextSize=14
aimButton.Font=Enum.Font.GothamBold
aimButton.Parent=aimPage

local aimButtonCorner=Instance.new("UICorner")
aimButtonCorner.CornerRadius=UDim.new(0,9)
aimButtonCorner.Parent=aimButton

aimButton.MouseButton1Click:Connect(function()
	aimOn=not aimOn
	if aimOn then
		aimButton.Text="AIM ON"
		aimButton.BackgroundColor3=Color3.fromRGB(45,170,90)
		updateTarget()
	else
		aimButton.Text="AIM OFF"
		aimButton.BackgroundColor3=Color3.fromRGB(90,90,100)
		currentTarget=nil
		currentDistance=math.huge
		aimStatus.Text="Status: OFF"
	end
end)

rangeBox.FocusLost:Connect(function()
	local value=tonumber(rangeBox.Text)
	if value and value>0 then
		MAX_RANGE=value
		updateTarget()
	else
		rangeBox.Text=tostring(MAX_RANGE)
	end
end)


espButton.MouseButton1Click:Connect(function()
	espOn=not espOn
	if espOn then
		espButton.Text="ESP ON"
		espButton.BackgroundColor3=Color3.fromRGB(45,170,90)
		updateESP()
	else
		espButton.Text="ESP OFF"
		espButton.BackgroundColor3=Color3.fromRGB(90,90,100)
		clearESP()
	end
end)


open.MouseButton1Click:Connect(function()
	frame.Visible=not frame.Visible
end)

settings.MouseButton1Click:Connect(function()
	settingsPage.Visible=true
	aimPage.Visible=false
	settings.BackgroundColor3=Color3.fromRGB(55,55,65)
	aim.BackgroundColor3=Color3.fromRGB(35,35,42)
end)

aim.MouseButton1Click:Connect(function()
	settingsPage.Visible=false
	aimPage.Visible=true
	settings.BackgroundColor3=Color3.fromRGB(35,35,42)
	aim.BackgroundColor3=Color3.fromRGB(55,55,65)
	updateTarget()
end)

flyButton.MouseButton1Click:Connect(function()
	flying=not flying
	if flying then
		flyButton.Text="FLY ON"
		flyButton.BackgroundColor3=Color3.fromRGB(45,170,90)
	else
		flyButton.Text="FLY OFF"
		flyButton.BackgroundColor3=Color3.fromRGB(90,90,100)
		local char=player.Character
		local hrp=char and char:FindFirstChild("HumanoidRootPart")
		if hrp then
			local bv=hrp:FindFirstChild("FlyVelocity")
			if bv then
				bv:Destroy()
			end
		end
	end
end)


speedButton.MouseButton1Click:Connect(function()
	speedOn=not speedOn
	local char=player.Character
	local humanoid=char and char:FindFirstChildOfClass("Humanoid")
	if speedOn then
		speedButton.Text="SPEED ON"
		speedButton.BackgroundColor3=Color3.fromRGB(45,170,90)
		if humanoid then
			humanoid.WalkSpeed=walkSpeed
		end
	else
		speedButton.Text="SPEED OFF"
		speedButton.BackgroundColor3=Color3.fromRGB(90,90,100)
		if humanoid then
			humanoid.WalkSpeed=16
		end
	end
end)

jumpButton.MouseButton1Click:Connect(function()
	jumpOn=not jumpOn
	local char=player.Character
	local humanoid=char and char:FindFirstChildOfClass("Humanoid")
	if jumpOn then
		jumpButton.Text="JUMP ON"
		jumpButton.BackgroundColor3=Color3.fromRGB(45,170,90)
		if humanoid then
			humanoid.UseJumpPower=true
			humanoid.JumpPower=jumpPower
		end
	else
		jumpButton.Text="JUMP OFF"
		jumpButton.BackgroundColor3=Color3.fromRGB(90,90,100)
		if humanoid then
			humanoid.UseJumpPower=true
			humanoid.JumpPower=50
		end
	end
end)


flyBox.FocusLost:Connect(function()
	local v=tonumber(flyBox.Text)
	if v and v>=1 then
		flySpeed=v
	else
		flyBox.Text=tostring(flySpeed)
	end
end)

speedBox.FocusLost:Connect(function()
	local v=tonumber(speedBox.Text)
	if v and v>=0 then
		walkSpeed=v
		if speedOn then
			local char=player.Character
			local hum=char and char:FindFirstChildOfClass("Humanoid")
			if hum then
				hum.WalkSpeed=walkSpeed
			end
		end
	else
		speedBox.Text=tostring(walkSpeed)
	end
end)

jumpBox.FocusLost:Connect(function()
	local v=tonumber(jumpBox.Text)
	if v and v>=0 then
		jumpPower=v
		if jumpOn then
			local char=player.Character
			local hum=char and char:FindFirstChildOfClass("Humanoid")
			if hum then
				hum.UseJumpPower=true
				hum.JumpPower=jumpPower
			end
		end
	else
		jumpBox.Text=tostring(jumpPower)
	end
end)

local confirm=Instance.new("Frame")
confirm.Size=UDim2.new(0,280,0,145)
confirm.Position=UDim2.new(0.5,-140,0.5,-72)
confirm.BackgroundColor3=Color3.fromRGB(25,25,30)
confirm.Visible=false
confirm.ZIndex=10
confirm.Parent=gui

local confirmCorner=Instance.new("UICorner")
confirmCorner.CornerRadius=UDim.new(0,13)
confirmCorner.Parent=confirm

local question=Instance.new("TextLabel")
question.Size=UDim2.new(1,-20,0,65)
question.Position=UDim2.new(0,10,0,10)
question.BackgroundTransparency=1
question.Text="Bạn có xác định tắt script?"
question.TextColor3=Color3.fromRGB(255,255,255)
question.TextSize=16
question.Font=Enum.Font.GothamBold
question.TextWrapped=true
question.ZIndex=11
question.Parent=confirm

local yes=Instance.new("TextButton")
yes.Size=UDim2.new(0,105,0,38)
yes.Position=UDim2.new(0,25,0,90)
yes.BackgroundColor3=Color3.fromRGB(180,55,55)
yes.Text="CÓ"
yes.TextColor3=Color3.fromRGB(255,255,255)
yes.TextSize=14
yes.Font=Enum.Font.GothamBold
yes.ZIndex=11
yes.Parent=confirm

local yesCorner=Instance.new("UICorner")
yesCorner.CornerRadius=UDim.new(0,8)
yesCorner.Parent=yes

local no=Instance.new("TextButton")
no.Size=UDim2.new(0,105,0,38)
no.Position=UDim2.new(0,150,0,90)
no.BackgroundColor3=Color3.fromRGB(60,60,70)
no.Text="KHÔNG"
no.TextColor3=Color3.fromRGB(255,255,255)
no.TextSize=14
no.Font=Enum.Font.GothamBold
no.ZIndex=11
no.Parent=confirm

local noCorner=Instance.new("UICorner")
noCorner.CornerRadius=UDim.new(0,8)
noCorner.Parent=no

close.MouseButton1Click:Connect(function()
	confirm.Visible=true
end)

no.MouseButton1Click:Connect(function()
	confirm.Visible=false
end)

yes.MouseButton1Click:Connect(function()
	flying=false
	speedOn=false
	jumpOn=false
	espOn=false
	aimOn=false
	currentTarget=nil
	clearESP()
	local char=player.Character
	local hrp=char and char:FindFirstChild("HumanoidRootPart")
	if hrp then
		local bv=hrp:FindFirstChild("FlyVelocity")
		if bv then
			bv:Destroy()
		end
	end
	if connection then
		connection:Disconnect()
		connection=nil
	end
	gui:Destroy()
end)


local dragging=false
local dragStart
local startPos

top.InputBegan:Connect(function(input)
	if input.UserInputType==Enum.UserInputType.MouseButton1 then
		dragging=true
		dragStart=input.Position
		startPos=frame.Position
	end
end)

top.InputEnded:Connect(function(input)
	if input.UserInputType==Enum.UserInputType.MouseButton1 then
		dragging=false
	end
end)

uis.InputChanged:Connect(function(input)
	if dragging and input.UserInputType==Enum.UserInputType.MouseMovement then
		local delta=input.Position-dragStart
		frame.Position=UDim2.new(
			startPos.X.Scale,
			startPos.X.Offset+delta.X,
			startPos.Y.Scale,
			startPos.Y.Offset+delta.Y
		)
	end
end)

local openDragging=false
local openDragStart
local openStartPos

open.InputBegan:Connect(function(input)
	if input.UserInputType==Enum.UserInputType.MouseButton1 then
		openDragging=true
		openDragStart=input.Position
		openStartPos=open.Position
	end
end)

open.InputEnded:Connect(function(input)
	if input.UserInputType==Enum.UserInputType.MouseButton1 then
		openDragging=false
	end
end)

uis.InputChanged:Connect(function(input)
	if openDragging and input.UserInputType==Enum.UserInputType.MouseMovement then
		local delta=input.Position-openDragStart
		open.Position=UDim2.new(
			openStartPos.X.Scale,
			openStartPos.X.Offset+delta.X,
			openStartPos.Y.Scale,
			openStartPos.Y.Offset+delta.Y
		)
	end
end)


player.CharacterAdded:Connect(function(char)
	local hum=char:WaitForChild("Humanoid")
	if speedOn then
		hum.WalkSpeed=walkSpeed
	end
	if jumpOn then
		hum.UseJumpPower=true
		hum.JumpPower=jumpPower
	end
	currentTarget=nil
end)

players.PlayerAdded:Connect(function(target)
	target.CharacterAdded:Connect(function()
		task.wait(0.3)
		if espOn then
			updateESP()
		end
	end)
end)

players.PlayerRemoving:Connect(function(target)
	if currentTarget==target then
		currentTarget=nil
		currentDistance=math.huge
	end
	if espOn then
		updateESP()
	end
end)

connection=run.RenderStepped:Connect(function()
	if flying then
		local char=player.Character
		local hrp=char and char:FindFirstChild("HumanoidRootPart")
		if hrp then
			local bv=hrp:FindFirstChild("FlyVelocity")
			if not bv then
				bv=Instance.new("BodyVelocity")
				bv.Name="FlyVelocity"
				bv.MaxForce=Vector3.new(
					math.huge,
					math.huge,
					math.huge
				)
				bv.Parent=hrp
			end
			local cam=workspace.CurrentCamera
			local move=Vector3.zero
			if uis:IsKeyDown(Enum.KeyCode.W) then
				move+=cam.CFrame.LookVector
			end
			if uis:IsKeyDown(Enum.KeyCode.S) then
				move-=cam.CFrame.LookVector
			end
			if uis:IsKeyDown(Enum.KeyCode.A) then
				move-=cam.CFrame.RightVector
			end
			if uis:IsKeyDown(Enum.KeyCode.D) then
				move+=cam.CFrame.RightVector
			end
			if uis:IsKeyDown(Enum.KeyCode.Space) then
				move+=Vector3.new(0,1,0)
			end
			if uis:IsKeyDown(Enum.KeyCode.LeftControl) then
				move-=Vector3.new(0,1,0)
			end
			if move.Magnitude>0 then
				bv.Velocity=move.Unit*flySpeed
			else
				bv.Velocity=Vector3.zero
			end
		end
	end
	if aimOn then
		updateTarget()
	end
end)


task.spawn(function()
	while gui.Parent do
		if espOn then
			updateESP()
		end
		task.wait(0.35)
	end
end)

task.spawn(function()
	while gui.Parent do
		local char=player.Character
		local hum=char and char:FindFirstChildOfClass("Humanoid")
		if hum then
			if speedOn and hum.WalkSpeed~=walkSpeed then
				hum.WalkSpeed=walkSpeed
			end
			if jumpOn and hum.JumpPower~=jumpPower then
				hum.UseJumpPower=true
				hum.JumpPower=jumpPower
			end
		end
		task.wait(0.1)
	end
end)

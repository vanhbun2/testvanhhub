local player=game.Players.LocalPlayer
local uis=game:GetService("UserInputService")
local run=game:GetService("RunService")
local players=game:GetService("Players")
local flying=false
local speedOn=false
local jumpOn=false
local espOn=false
local aimOn=false
local firing=false
local flySpeed=60
local walkSpeed=60
local jumpPower=50
local minRange=20
local maxRange=300
local aimRange=120
local currentTarget=nil
local currentDistance=math.huge
local gui=Instance.new("ScreenGui")
gui.Name="Vanh"
gui.ResetOnSpawn=false
gui.Parent=player:WaitForChild("PlayerGui")
local aimCircle=Instance.new("Frame")
aimCircle.Name="AimCircle"
aimCircle.AnchorPoint=Vector2.new(0.5,0.5)
aimCircle.Position=UDim2.fromScale(0.5,0.5)
aimCircle.Size=UDim2.fromOffset(aimRange*2,aimRange*2)
aimCircle.BackgroundTransparency=1
aimCircle.Visible=false
aimCircle.Parent=gui
local circleCorner=Instance.new("UICorner")
circleCorner.CornerRadius=UDim.new(1,0)
circleCorner.Parent=aimCircle
local circleStroke=Instance.new("UIStroke")
circleStroke.Thickness=2
circleStroke.Parent=aimCircle
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
frame.Size=UDim2.new(0,330,0,430)
frame.Position=UDim2.new(0.5,-165,0.5,-215)
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
local aimTab=Instance.new("TextButton")
aimTab.Size=UDim2.new(0,140,0,38)
aimTab.Position=UDim2.new(0,170,0,70)
aimTab.BackgroundColor3=Color3.fromRGB(35,35,42)
aimTab.Text="AIM"
aimTab.TextColor3=Color3.fromRGB(180,180,190)
aimTab.TextSize=14
aimTab.Font=Enum.Font.GothamBold
aimTab.Parent=frame
local aimTabCorner=Instance.new("UICorner")
aimTabCorner.CornerRadius=UDim.new(0,9)
aimTabCorner.Parent=aimTab
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
local function button(text,y)
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
local function box(value,y)
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
local flyButton=button("FLY OFF",0)
local flyBox=box(flySpeed,0)
local speedButton=button("SPEED OFF",55)
local speedBox=box(walkSpeed,55)
local jumpButton=button("JUMP OFF",110)
local jumpBox=box(jumpPower,110)
local espButton=button("ESP OFF",165)
local espFolder=Instance.new("Folder")
espFolder.Name="VanhESP"
espFolder.Parent=gui
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
	for _,p in ipairs(players:GetPlayers()) do
		if p~=player then
			local char=p.Character
			local hum=char and char:FindFirstChildOfClass("Humanoid")
			local head=char and char:FindFirstChild("Head")
			if hum and hum.Health>0 then
				local h=Instance.new("Highlight")
				h.Adornee=char
				h.FillTransparency=0.65
				h.OutlineTransparency=0
				h.Parent=espFolder
				if head then
					local bb=Instance.new("BillboardGui")
					bb.Adornee=head
					bb.Size=UDim2.new(0,220,0,45)
					bb.StudsOffset=Vector3.new(0,3,0)
					bb.AlwaysOnTop=true
					bb.Parent=espFolder
					local label=Instance.new("TextLabel")
					label.Size=UDim2.fromScale(1,1)
					label.BackgroundTransparency=1
					label.Text=p.DisplayName
					label.TextColor3=Color3.fromRGB(255,255,255)
					label.TextStrokeTransparency=0
					label.TextSize=14
					label.Font=Enum.Font.GothamBold
					label.Parent=bb
				end
			end
		end
	end
end
local aimTitle=Instance.new("TextLabel")
aimTitle.Size=UDim2.new(1,0,0,30)
aimTitle.Position=UDim2.new(0,0,0,5)
aimTitle.BackgroundTransparency=1
aimTitle.Text="AIM • TARGET SELECTOR"
aimTitle.TextColor3=Color3.fromRGB(150,150,160)
aimTitle.TextSize=16
aimTitle.Font=Enum.Font.GothamBold
aimTitle.Parent=aimPage
local aimStatus=Instance.new("TextLabel")
aimStatus.Size=UDim2.new(1,0,0,60)
aimStatus.Position=UDim2.new(0,0,0,38)
aimStatus.BackgroundTransparency=1
aimStatus.Text="Status: OFF\nTarget: NONE"
aimStatus.TextColor3=Color3.fromRGB(180,180,190)
aimStatus.TextSize=12
aimStatus.Font=Enum.Font.Gotham
aimStatus.TextXAlignment=Enum.TextXAlignment.Left
aimStatus.TextYAlignment=Enum.TextYAlignment.Top
aimStatus.Parent=aimPage
local rangeText=Instance.new("TextLabel")
rangeText.Size=UDim2.new(1,0,0,25)
rangeText.Position=UDim2.new(0,0,0,90)
rangeText.BackgroundTransparency=1
rangeText.Text="AIM RANGE: "..aimRange
rangeText.TextColor3=Color3.fromRGB(255,255,255)
rangeText.TextSize=13
rangeText.Font=Enum.Font.GothamBold
rangeText.TextXAlignment=Enum.TextXAlignment.Left
rangeText.Parent=aimPage
local slider=Instance.new("Frame")
slider.Size=UDim2.new(1,0,0,8)
slider.Position=UDim2.new(0,0,0,120)
slider.BackgroundColor3=Color3.fromRGB(55,55,65)
slider.Parent=aimPage
local sliderCorner=Instance.new("UICorner")
sliderCorner.CornerRadius=UDim.new(1,0)
sliderCorner.Parent=slider
local fill=Instance.new("Frame")
fill.Size=UDim2.new((aimRange-minRange)/(maxRange-minRange),0,1,0)
fill.BackgroundColor3=Color3.fromRGB(80,170,255)
fill.Parent=slider
local fillCorner=Instance.new("UICorner")
fillCorner.CornerRadius=UDim.new(1,0)
fillCorner.Parent=fill
local knob=Instance.new("TextButton")
knob.Size=UDim2.new(0,18,0,18)
knob.AnchorPoint=Vector2.new(0.5,0.5)
knob.Position=UDim2.new((aimRange-minRange)/(maxRange-minRange),0,0.5,0)
knob.BackgroundColor3=Color3.fromRGB(255,255,255)
knob.Text=""
knob.Parent=slider
local knobCorner=Instance.new("UICorner")
knobCorner.CornerRadius=UDim.new(1,0)
knobCorner.Parent=knob
local sliderDragging=false
local function setRange(x)
	local alpha=math.clamp((x-slider.AbsolutePosition.X)/slider.AbsoluteSize.X,0,1)
	aimRange=math.floor(minRange+(maxRange-minRange)*alpha)
	local percent=(aimRange-minRange)/(maxRange-minRange)
	fill.Size=UDim2.new(percent,0,1,0)
	knob.Position=UDim2.new(percent,0,0.5,0)
	rangeText.Text="AIM RANGE: "..aimRange
	aimCircle.Size=UDim2.fromOffset(aimRange*2,aimRange*2)
end
knob.MouseButton1Down:Connect(function()
	sliderDragging=true
end)
slider.InputBegan:Connect(function(input)
	if input.UserInputType==Enum.UserInputType.MouseButton1 then
		sliderDragging=true
		setRange(input.Position.X)
	end
end)
uis.InputChanged:Connect(function(input)
	if sliderDragging and input.UserInputType==Enum.UserInputType.MouseMovement then
		setRange(input.Position.X)
	end
end)
uis.InputEnded:Connect(function(input)
	if input.UserInputType==Enum.UserInputType.MouseButton1 then
		sliderDragging=false
	end
end)
local aimButton=Instance.new("TextButton")
aimButton.Size=UDim2.new(1,0,0,40)
aimButton.Position=UDim2.new(0,0,0,150)
aimButton.BackgroundColor3=Color3.fromRGB(90,90,100)
aimButton.Text="AIM OFF"
aimButton.TextColor3=Color3.fromRGB(255,255,255)
aimButton.TextSize=14
aimButton.Font=Enum.Font.GothamBold
aimButton.Parent=aimPage
local aimButtonCorner=Instance.new("UICorner")
aimButtonCorner.CornerRadius=UDim.new(0,9)
aimButtonCorner.Parent=aimButton
local function isEnemy(p)
	if p==player then
		return false
	end
	if not p:IsA("Player") then
		return false
	end
	if player.Team and p.Team and player.Team==p.Team then
		return false
	end
	local char=p.Character
	local hum=char and char:FindFirstChildOfClass("Humanoid")
	local root=char and char:FindFirstChild("HumanoidRootPart")
	if not hum or not root or hum.Health<=0 then
		return false
	end
	return true
end
local function getNearestTarget()
	local char=player.Character
	local root=char and char:FindFirstChild("HumanoidRootPart")
	if not root then
		return nil,math.huge
	end
	local best=nil
	local bestDistance=aimRange
	for _,p in ipairs(players:GetPlayers()) do
		if isEnemy(p) then
			local targetChar=p.Character
			local targetRoot=targetChar and targetChar:FindFirstChild("HumanoidRootPart")
			if targetRoot then
				local distance=(targetRoot.Position-root.Position).Magnitude
				if distance<=bestDistance then
					best=p
					bestDistance=distance
				end
			end
		end
	end
	return best,bestDistance
end
local function updateTarget()
	if not aimOn or not firing then
		currentTarget=nil
		currentDistance=math.huge
		if aimOn then
			aimStatus.Text="Status: ON\nTarget: NONE"
		else
			aimStatus.Text="Status: OFF\nTarget: NONE"
		end
		return
	end
	local target,distance=getNearestTarget()
	currentTarget=target
	currentDistance=distance
	if target then
		aimStatus.Text="Status: ON\nTarget: "..target.DisplayName.."\nDistance: "..math.floor(distance)
	else
		aimStatus.Text="Status: ON\nTarget: NONE"
	end
end
aimButton.MouseButton1Click:Connect(function()
	aimOn=not aimOn
	aimCircle.Visible=aimOn
	if aimOn then
		aimButton.Text="AIM ON"
		aimButton.BackgroundColor3=Color3.fromRGB(45,170,90)
		aimStatus.Text="Status: ON\nTarget: NONE"
	else
		aimButton.Text="AIM OFF"
		aimButton.BackgroundColor3=Color3.fromRGB(90,90,100)
		currentTarget=nil
		currentDistance=math.huge
		aimStatus.Text="Status: OFF\nTarget: NONE"
	end
end)
uis.InputBegan:Connect(function(input,processed)
	if processed then
		return
	end
	if input.UserInputType==Enum.UserInputType.MouseButton1 then
		firing=true
		updateTarget()
	end
end)
uis.InputEnded:Connect(function(input)
	if input.UserInputType==Enum.UserInputType.MouseButton1 then
		firing=false
		currentTarget=nil
		currentDistance=math.huge
	end
end)
settings.MouseButton1Click:Connect(function()
	settingsPage.Visible=true
	aimPage.Visible=false
	settings.BackgroundColor3=Color3.fromRGB(55,55,65)
	aimTab.BackgroundColor3=Color3.fromRGB(35,35,42)
end)
aimTab.MouseButton1Click:Connect(function()
	settingsPage.Visible=false
	aimPage.Visible=true
	settings.BackgroundColor3=Color3.fromRGB(35,35,42)
	aimTab.BackgroundColor3=Color3.fromRGB(55,55,65)
end)
open.MouseButton1Click:Connect(function()
	frame.Visible=not frame.Visible
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
		local root=char and char:FindFirstChild("HumanoidRootPart")
		local bv=root and root:FindFirstChild("VanhFly")
		if bv then
			bv:Destroy()
		end
	end
end)
speedButton.MouseButton1Click:Connect(function()
	speedOn=not speedOn
	local char=player.Character
	local hum=char and char:FindFirstChildOfClass("Humanoid")
	if speedOn then
		speedButton.Text="SPEED ON"
		speedButton.BackgroundColor3=Color3.fromRGB(45,170,90)
		if hum then
			hum.WalkSpeed=walkSpeed
		end
	else
		speedButton.Text="SPEED OFF"
		speedButton.BackgroundColor3=Color3.fromRGB(90,90,100)
		if hum then
			hum.WalkSpeed=16
		end
	end
end)
jumpButton.MouseButton1Click:Connect(function()
	jumpOn=not jumpOn
	local char=player.Character
	local hum=char and char:FindFirstChildOfClass("Humanoid")
	if jumpOn then
		jumpButton.Text="JUMP ON"
		jumpButton.BackgroundColor3=Color3.fromRGB(45,170,90)
		if hum then
			hum.UseJumpPower=true
			hum.JumpPower=jumpPower
		end
	else
		jumpButton.Text="JUMP OFF"
		jumpButton.BackgroundColor3=Color3.fromRGB(90,90,100)
		if hum then
			hum.UseJumpPower=true
			hum.JumpPower=50
		end
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
flyBox.FocusLost:Connect(function()
	local v=tonumber(flyBox.Text)
	if v and v>0 then
		flySpeed=v
	else
		flyBox.Text=tostring(flySpeed)
	end
end)
speedBox.FocusLost:Connect(function()
	local v=tonumber(speedBox.Text)
	if v and v>=0 then
		walkSpeed=v
	else
		speedBox.Text=tostring(walkSpeed)
	end
end)
jumpBox.FocusLost:Connect(function()
	local v=tonumber(jumpBox.Text)
	if v and v>=0 then
		jumpPower=v
	else
		jumpBox.Text=tostring(jumpPower)
	end
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
		frame.Position=UDim2.new(startPos.X.Scale,startPos.X.Offset+delta.X,startPos.Y.Scale,startPos.Y.Offset+delta.Y)
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
		open.Position=UDim2.new(openStartPos.X.Scale,openStartPos.X.Offset+delta.X,openStartPos.Y.Scale,openStartPos.Y.Offset+delta.Y)
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
end)
run.RenderStepped:Connect(function()
	if aimCircle.Visible then
		local camera=workspace.CurrentCamera
		aimCircle.Position=UDim2.fromOffset(camera.ViewportSize.X/2,camera.ViewportSize.Y/2)
		aimCircle.Size=UDim2.fromOffset(aimRange*2,aimRange*2)
	end
	if aimOn and firing then
		updateTarget()
	end
	if flying then
		local char=player.Character
		local root=char and char:FindFirstChild("HumanoidRootPart")
		if root then
			local bv=root:FindFirstChild("VanhFly")
			if not bv then
				bv=Instance.new("BodyVelocity")
				bv.Name="VanhFly"
				bv.MaxForce=Vector3.new(math.huge,math.huge,math.huge)
				bv.Parent=root
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
			bv.Velocity=move.Magnitude>0 and move.Unit*flySpeed or Vector3.zero
		end
	end
end)
task.spawn(function()
	while gui.Parent do
		if espOn then
			updateESP()
		end
		task.wait(0.5)
	end
end)

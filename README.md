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
local aimRange=100
local connection

local gui=Instance.new("ScreenGui")
gui.Name="Vanh"
gui.ResetOnSpawn=false
gui.Parent=player:WaitForChild("PlayerGui")

local function corner(obj,r)
	local c=Instance.new("UICorner")
	c.CornerRadius=UDim.new(0,r)
	c.Parent=obj
end

local function button(parent,text,pos,size)
	local b=Instance.new("TextButton")
	b.Size=size
	b.Position=pos
	b.BackgroundColor3=Color3.fromRGB(90,90,100)
	b.Text=text
	b.TextColor3=Color3.fromRGB(255,255,255)
	b.TextSize=13
	b.Font=Enum.Font.GothamBold
	b.Parent=parent
	corner(b,9)
	return b
end

local open=button(gui,"VANH",UDim2.new(0,20,.5,-21),UDim2.new(0,85,0,42))
open.BackgroundColor3=Color3.fromRGB(25,25,30)
open.TextSize=18

local frame=Instance.new("Frame")
frame.Size=UDim2.new(0,330,0,410)
frame.Position=UDim2.new(.5,-165,.5,-205)
frame.BackgroundColor3=Color3.fromRGB(20,20,25)
frame.Visible=false
frame.Parent=gui
corner(frame,15)

local top=Instance.new("Frame")
top.Size=UDim2.new(1,0,0,55)
top.BackgroundColor3=Color3.fromRGB(28,28,34)
top.Parent=frame
corner(top,15)

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

local close=button(top,"X",UDim2.new(1,-45,0,10),UDim2.new(0,35,0,35))
close.BackgroundColor3=Color3.fromRGB(180,55,55)

local settings=button(frame,"SETTINGS",UDim2.new(0,20,0,70),UDim2.new(0,140,0,38))

local aim=button(frame,"AIM",UDim2.new(0,170,0,70),UDim2.new(0,140,0,38))
aim.BackgroundColor3=Color3.fromRGB(35,35,42)
aim.TextColor3=Color3.fromRGB(180,180,190)

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

local function settingButton(text,y)
	return button(settingsPage,text,UDim2.new(0,0,0,y),UDim2.new(0,100,0,38))
end

local function settingBox(value,y)
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
	corner(b,9)
	return b
end

local flyButton=settingButton("FLY OFF",0)
local flyBox=settingBox(flySpeed,0)
local speedButton=settingButton("SPEED OFF",55)
local speedBox=settingBox(walkSpeed,55)
local jumpButton=settingButton("JUMP OFF",110)
local jumpBox=settingBox(jumpPower,110)
local espButton=settingButton("ESP OFF",165)

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
	if not espOn then return end
	for _,p in ipairs(players:GetPlayers()) do
		if p~=player and p.Character then
			local char=p.Character
			local hum=char:FindFirstChildOfClass("Humanoid")
			local head=char:FindFirstChild("Head")
			if hum then
				local h=Instance.new("Highlight")
				h.Adornee=char
				h.FillTransparency=.65
				h.OutlineTransparency=0
				h.Parent=espFolder
				if head then
					local bb=Instance.new("BillboardGui")
					bb.Adornee=head
					bb.Size=UDim2.new(0,250,0,55)
					bb.StudsOffset=Vector3.new(0,3,0)
					bb.AlwaysOnTop=true
					bb.Parent=espFolder
					local t=Instance.new("TextLabel")
					t.Size=UDim2.new(1,0,1,0)
					t.BackgroundTransparency=1
					t.TextColor3=Color3.fromRGB(255,255,255)
					t.TextStrokeTransparency=0
					t.TextSize=14
					t.Font=Enum.Font.GothamBold
					t.Text=p.DisplayName.." ["..p.Name.."]\nHP: "..math.floor(hum.Health).."/"..math.floor(hum.MaxHealth)
					t.Parent=bb
				end
			end
		end
	end
end

local aimTitle=Instance.new("TextLabel")
aimTitle.Size=UDim2.new(1,0,0,30)
aimTitle.Position=UDim2.new(0,0,0,5)
aimTitle.BackgroundTransparency=1
aimTitle.Text="AIM • PLAYER"
aimTitle.TextColor3=Color3.fromRGB(150,150,160)
aimTitle.TextSize=16
aimTitle.Font=Enum.Font.GothamBold
aimTitle.Parent=aimPage

local aimStatus=Instance.new("TextLabel")
aimStatus.Size=UDim2.new(1,0,0,100)
aimStatus.Position=UDim2.new(0,0,0,45)
aimStatus.BackgroundTransparency=1
aimStatus.Text="Status: OFF\nTarget: NONE"
aimStatus.TextColor3=Color3.fromRGB(180,180,190)
aimStatus.TextSize=12
aimStatus.Font=Enum.Font.Gotham
aimStatus.TextYAlignment=Enum.TextYAlignment.Top
aimStatus.Parent=aimPage

local aimButton=button(aimPage,"AIM OFF",UDim2.new(0,0,0,155),UDim2.new(0,290,0,40))

local confirm=Instance.new("Frame")
confirm.Size=UDim2.new(0,280,0,145)
confirm.Position=UDim2.new(.5,-140,.5,-72)
confirm.BackgroundColor3=Color3.fromRGB(25,25,30)
confirm.Visible=false
confirm.ZIndex=10
confirm.Parent=gui
corner(confirm,13)

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

local yes=button(confirm,"CÓ",UDim2.new(0,25,0,90),UDim2.new(0,105,0,38))
yes.BackgroundColor3=Color3.fromRGB(180,55,55)
yes.ZIndex=11

local no=button(confirm,"KHÔNG",UDim2.new(0,150,0,90),UDim2.new(0,105,0,38))
no.ZIndex=11

local function setState(b,onText,offText,state)
	b.Text=state and onText or offText
	b.BackgroundColor3=state and Color3.fromRGB(45,170,90) or Color3.fromRGB(90,90,100)
end

local function nearestPlayer()
	local char=player.Character
	local root=char and char:FindFirstChild("HumanoidRootPart")
	if not root then return nil end
	local target=nil
	local distance=aimRange
	for _,p in ipairs(players:GetPlayers()) do
		if p~=player then
			local c=p.Character
			local r=c and c:FindFirstChild("HumanoidRootPart")
			local h=c and c:FindFirstChildOfClass("Humanoid")
			if r and h and h.Health>0 then
				local d=(r.Position-root.Position).Magnitude
				if d<distance then
					distance=d
					target=p
				end
			end
		end
	end
	return target
end

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
end)

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
	local char=player.Character
	local root=char and char:FindFirstChild("HumanoidRootPart")
	local hum=char and char:FindFirstChildOfClass("Humanoid")
	if root then
		local bv=root:FindFirstChild("FlyVelocity")
		if bv then bv:Destroy() end
		local sv=root:FindFirstChild("SpeedVel")
		if sv then sv:Destroy() end
	end
	if hum then
		hum.WalkSpeed=16
		hum.UseJumpPower=true
		hum.JumpPower=50
	end
	if connection then
		connection:Disconnect()
		connection=nil
	end
	clearESP()
	gui:Destroy()
end)

flyButton.MouseButton1Click:Connect(function()
	flying=not flying
	setState(flyButton,"FLY ON","FLY OFF",flying)
	if not flying then
		local char=player.Character
		local root=char and char:FindFirstChild("HumanoidRootPart")
		local bv=root and root:FindFirstChild("FlyVelocity")
		if bv then bv:Destroy() end
	end
end)

speedButton.MouseButton1Click:Connect(function()
	speedOn=not speedOn
	local char=player.Character
	local hrp=char and char:FindFirstChild("HumanoidRootPart")
	local humanoid=char and char:FindFirstChildOfClass("Humanoid")
	if speedOn then
		speedButton.Text="SPEED ON"
		speedButton.BackgroundColor3=Color3.fromRGB(45,170,90)
		if humanoid then
			humanoid.WalkSpeed=walkSpeed
		end
		if hrp then
			local bv=hrp:FindFirstChild("SpeedVel")
			if not bv then
				bv=Instance.new("BodyVelocity")
				bv.Name="SpeedVel"
				bv.MaxForce=Vector3.new(math.huge,0,math.huge)
				bv.Velocity=Vector3.zero
				bv.Parent=hrp
			end
			task.spawn(function()
				while speedOn and hrp and hrp.Parent do
					if humanoid and humanoid.MoveDirection.Magnitude>0 then
						bv.Velocity=humanoid.MoveDirection*(walkSpeed-16)
					else
						bv.Velocity=Vector3.zero
					end
					task.wait(0.01)
				end
				if bv and bv.Parent then
					bv:Destroy()
				end
			end)
		end
	else
		speedButton.Text="SPEED OFF"
		speedButton.BackgroundColor3=Color3.fromRGB(90,90,100)
		if humanoid then
			humanoid.WalkSpeed=16
		end
		if hrp then
			local bv=hrp:FindFirstChild("SpeedVel")
			if bv then bv:Destroy() end
		end
	end
end)

jumpButton.MouseButton1Click:Connect(function()
	jumpOn=not jumpOn
	setState(jumpButton,"JUMP ON","JUMP OFF",jumpOn)
	local char=player.Character
	local humanoid=char and char:FindFirstChildOfClass("Humanoid")
	if humanoid then
		humanoid.UseJumpPower=true
		humanoid.JumpPower=jumpOn and jumpPower or 50
	end
end)

espButton.MouseButton1Click:Connect(function()
	espOn=not espOn
	setState(espButton,"ESP ON","ESP OFF",espOn)
	if espOn then
		updateESP()
	else
		clearESP()
	end
end)

aimButton.MouseButton1Click:Connect(function()
	aimOn=not aimOn
	setState(aimButton,"AIM ON","AIM OFF",aimOn)
	if not aimOn then
		aimStatus.Text="Status: OFF\nTarget: NONE"
	end
end)

flyBox.FocusLost:Connect(function()
	local value=tonumber(flyBox.Text)
	if value and value>=1 then
		flySpeed=value
	else
		flyBox.Text=tostring(flySpeed)
	end
end)

speedBox.FocusLost:Connect(function()
	local value=tonumber(speedBox.Text)
	if value and value>=0 then
		walkSpeed=value
		if speedOn then
			local char=player.Character
			local humanoid=char and char:FindFirstChildOfClass("Humanoid")
			if humanoid then
				humanoid.WalkSpeed=walkSpeed
			end
		end
	else
		speedBox.Text=tostring(walkSpeed)
	end
end)

jumpBox.FocusLost:Connect(function()
	local value=tonumber(jumpBox.Text)
	if value and value>=0 then
		jumpPower=value
		if jumpOn then
			local char=player.Character
			local humanoid=char and char:FindFirstChildOfClass("Humanoid")
			if humanoid then
				humanoid.UseJumpPower=true
				humanoid.JumpPower=jumpPower
			end
		end
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
	local humanoid=char:WaitForChild("Humanoid")
	if speedOn then
		humanoid.WalkSpeed=walkSpeed
	end
	if jumpOn then
		humanoid.UseJumpPower=true
		humanoid.JumpPower=jumpPower
	end
end)

players.PlayerAdded:Connect(function(p)
	p.CharacterAdded:Connect(function()
		task.wait(.3)
		if espOn then
			updateESP()
		end
	end)
end)

players.PlayerRemoving:Connect(function()
	if espOn then
		updateESP()
	end
end)

connection=run.RenderStepped:Connect(function()
	local char=player.Character
	local hrp=char and char:FindFirstChild("HumanoidRootPart")
	if flying and hrp then
		local bv=hrp:FindFirstChild("FlyVelocity")
		if not bv then
			bv=Instance.new("BodyVelocity")
			bv.Name="FlyVelocity"
			bv.MaxForce=Vector3.new(math.huge,math.huge,math.huge)
			bv.Parent=hrp
		end
		local cam=workspace.CurrentCamera
		local move=Vector3.zero
		if uis:IsKeyDown(Enum.KeyCode.W) then move+=cam.CFrame.LookVector end
		if uis:IsKeyDown(Enum.KeyCode.S) then move-=cam.CFrame.LookVector end
		if uis:IsKeyDown(Enum.KeyCode.A) then move-=cam.CFrame.RightVector end
		if uis:IsKeyDown(Enum.KeyCode.D) then move+=cam.CFrame.RightVector end
		if uis:IsKeyDown(Enum.KeyCode.Space) then move+=Vector3.new(0,1,0) end
		if uis:IsKeyDown(Enum.KeyCode.LeftControl) then move-=Vector3.new(0,1,0) end
		bv.Velocity=move.Magnitude>0 and move.Unit*flySpeed or Vector3.zero
	end
	if aimOn then
		local target=nearestPlayer()
		local cam=workspace.CurrentCamera
		local targetRoot=target and target.Character and target.Character:FindFirstChild("HumanoidRootPart")
		if targetRoot and cam then
			cam.CFrame=CFrame.lookAt(cam.CFrame.Position,targetRoot.Position)
			aimStatus.Text="Status: ON\nTarget: "..target.Name
		else
			aimStatus.Text="Status: ON\nTarget: NONE"
		end
	end
end)

task.spawn(function()
	while gui.Parent do
		if espOn then
			updateESP()
		end
		task.wait(.35)
	end
end)

task.spawn(function()
	while gui.Parent do
		local char=player.Character
		local humanoid=char and char:FindFirstChildOfClass("Humanoid")
		if humanoid then
			if speedOn and humanoid.WalkSpeed~=walkSpeed then
				humanoid.WalkSpeed=walkSpeed
			end
			if jumpOn and humanoid.JumpPower~=jumpPower then
				humanoid.UseJumpPower=true
				humanoid.JumpPower=jumpPower
			end
		end
		task.wait(.1)
	end
end)

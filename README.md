local player=game.Players.LocalPlayer
local players=game:GetService("Players")
local uis=game:GetService("UserInputService")
local run=game:GetService("RunService")
local flying=false
local speedOn=false
local jumpOn=false
local espOn=false
local aimOn=false
local flySpeed=60
local walkSpeed=60
local jumpPower=50
local aimRange=100
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
local oc=Instance.new("UICorner")
oc.CornerRadius=UDim.new(0,10)
oc.Parent=open
local frame=Instance.new("Frame")
frame.Size=UDim2.new(0,330,0,410)
frame.Position=UDim2.new(0.5,-165,0.5,-205)
frame.BackgroundColor3=Color3.fromRGB(20,20,25)
frame.Visible=false
frame.Parent=gui
local fc=Instance.new("UICorner")
fc.CornerRadius=UDim.new(0,15)
fc.Parent=frame
local top=Instance.new("Frame")
top.Size=UDim2.new(1,0,0,55)
top.BackgroundColor3=Color3.fromRGB(28,28,34)
top.Parent=frame
local tc=Instance.new("UICorner")
tc.CornerRadius=UDim.new(0,15)
tc.Parent=top
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
local cc=Instance.new("UICorner")
cc.CornerRadius=UDim.new(0,9)
cc.Parent=close
local confirm=Instance.new("Frame")
confirm.Size=UDim2.new(0,280,0,145)
confirm.Position=UDim2.new(0.5,-140,0.5,-72)
confirm.BackgroundColor3=Color3.fromRGB(30,30,36)
confirm.Visible=false
confirm.ZIndex=20
confirm.Parent=gui
local confirmCorner=Instance.new("UICorner")
confirmCorner.CornerRadius=UDim.new(0,12)
confirmCorner.Parent=confirm
local confirmTitle=Instance.new("TextLabel")
confirmTitle.Size=UDim2.new(1,-20,0,35)
confirmTitle.Position=UDim2.new(0,10,0,12)
confirmTitle.BackgroundTransparency=1
confirmTitle.Text="XÁC NHẬN"
confirmTitle.TextColor3=Color3.fromRGB(255,255,255)
confirmTitle.TextSize=18
confirmTitle.Font=Enum.Font.GothamBold
confirmTitle.ZIndex=21
confirmTitle.Parent=confirm
local confirmText=Instance.new("TextLabel")
confirmText.Size=UDim2.new(1,-20,0,35)
confirmText.Position=UDim2.new(0,10,0,47)
confirmText.BackgroundTransparency=1
confirmText.Text="Bạn có xác định tắt script?"
confirmText.TextColor3=Color3.fromRGB(190,190,200)
confirmText.TextSize=13
confirmText.Font=Enum.Font.Gotham
confirmText.ZIndex=21
confirmText.Parent=confirm
local yes=Instance.new("TextButton")
yes.Size=UDim2.new(0,105,0,35)
yes.Position=UDim2.new(0,25,1,-48)
yes.BackgroundColor3=Color3.fromRGB(180,55,55)
yes.Text="CÓ"
yes.TextColor3=Color3.fromRGB(255,255,255)
yes.TextSize=13
yes.Font=Enum.Font.GothamBold
yes.ZIndex=21
yes.Parent=confirm
local yesCorner=Instance.new("UICorner")
yesCorner.CornerRadius=UDim.new(0,8)
yesCorner.Parent=yes
local no=Instance.new("TextButton")
no.Size=UDim2.new(0,105,0,35)
no.Position=UDim2.new(1,-130,1,-48)
no.BackgroundColor3=Color3.fromRGB(55,55,65)
no.Text="KHÔNG"
no.TextColor3=Color3.fromRGB(255,255,255)
no.TextSize=13
no.Font=Enum.Font.GothamBold
no.ZIndex=21
no.Parent=confirm
local noCorner=Instance.new("UICorner")
noCorner.CornerRadius=UDim.new(0,8)
noCorner.Parent=no
local settings=Instance.new("TextButton")
settings.Size=UDim2.new(0,140,0,38)
settings.Position=UDim2.new(0,20,0,70)
settings.BackgroundColor3=Color3.fromRGB(55,55,65)
settings.Text="SETTINGS"
settings.TextColor3=Color3.fromRGB(255,255,255)
settings.TextSize=14
settings.Font=Enum.Font.GothamBold
settings.Parent=frame
local sc=Instance.new("UICorner")
sc.CornerRadius=UDim.new(0,9)
sc.Parent=settings
local aim=Instance.new("TextButton")
aim.Size=UDim2.new(0,140,0,38)
aim.Position=UDim2.new(0,170,0,70)
aim.BackgroundColor3=Color3.fromRGB(35,35,42)
aim.Text="AIM"
aim.TextColor3=Color3.fromRGB(180,180,190)
aim.TextSize=14
aim.Font=Enum.Font.GothamBold
aim.Parent=frame
local ac=Instance.new("UICorner")
ac.CornerRadius=UDim.new(0,9)
ac.Parent=aim
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
local function makeESP(target)
	if not espOn or target==player then return end
	local char=target.Character
	if not char then return end
	local hum=char:FindFirstChildOfClass("Humanoid")
	local head=char:FindFirstChild("Head")
	if not hum then return end
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
		t.Text=target.DisplayName.." ["..target.Name.."]\nHP: "..math.floor(hum.Health).."/"..math.floor(hum.MaxHealth)
		t.Parent=bb
	end
end
local function updateESP()
	clearESP()
	if not espOn then return end
	for _,p in ipairs(players:GetPlayers()) do
		makeESP(p)
	end
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
aimStatus.Size=UDim2.new(0,290,0,100)
aimStatus.Position=UDim2.new(0,0,0,45)
aimStatus.BackgroundTransparency=1
aimStatus.Text="Status: OFF\nTarget: NONE"
aimStatus.TextColor3=Color3.fromRGB(180,180,190)
aimStatus.TextSize=12
aimStatus.Font=Enum.Font.Gotham
aimStatus.TextWrapped=true
aimStatus.TextYAlignment=Enum.TextYAlignment.Top
aimStatus.Parent=aimPage
local aimButton=Instance.new("TextButton")
aimButton.Size=UDim2.new(0,290,0,40)
aimButton.Position=UDim2.new(0,0,0,155)
aimButton.BackgroundColor3=Color3.fromRGB(90,90,100)
aimButton.Text="AIM OFF"
aimButton.TextColor3=Color3.fromRGB(255,255,255)
aimButton.TextSize=14
aimButton.Font=Enum.Font.GothamBold
aimButton.Parent=aimPage
local abc=Instance.new("UICorner")
abc.CornerRadius=UDim.new(0,9)
abc.Parent=aimButton
aimButton.MouseButton1Click:Connect(function()
	aimOn=not aimOn
	if aimOn then
		aimButton.Text="AIM ON"
		aimButton.BackgroundColor3=Color3.fromRGB(45,170,90)
	else
		aimButton.Text="AIM OFF"
		aimButton.BackgroundColor3=Color3.fromRGB(90,90,100)
		aimStatus.Text="Status: OFF\nTarget: NONE"
	end
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
open.MouseButton1Click:Connect(function()
	frame.Visible=not frame.Visible
end)
close.MouseButton1Click:Connect(function()
	confirm.Visible=true
end)
no.MouseButton1Click:Connect(function()
	confirm.Visible=false
end)
yes.MouseButton1Click:Connect(function()
	local char=player.Character
	local root=char and char:FindFirstChild("HumanoidRootPart")
	local hum=char and char:FindFirstChildOfClass("Humanoid")
	flying=false
	speedOn=false
	jumpOn=false
	aimOn=false
	espOn=false
	if root then
		local bv=root:FindFirstChild("FlyVelocity")
		if bv then bv:Destroy() end
	end
	if hum then
		hum.WalkSpeed=16
		hum.UseJumpPower=true
		hum.JumpPower=50
	end
	clearESP()
	gui:Destroy()
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
		local bv=root and root:FindFirstChild("FlyVelocity")
		if bv then bv:Destroy() end
	end
end)
speedButton.MouseButton1Click:Connect(function()
	speedOn=not speedOn
	local char=player.Character
	local hum=char and char:FindFirstChildOfClass("Humanoid")
	if speedOn then
		speedButton.Text="SPEED ON"
		speedButton.BackgroundColor3=Color3.fromRGB(45,170,90)
		if hum then hum.WalkSpeed=walkSpeed end
	else
		speedButton.Text="SPEED OFF"
		speedButton.BackgroundColor3=Color3.fromRGB(90,90,100)
		if hum then hum.WalkSpeed=16 end
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
	if v and v>=1 then
		walkSpeed=v
		if speedOn then
			local char=player.Character
			local hum=char and char:FindFirstChildOfClass("Humanoid")
			if hum then hum.WalkSpeed=walkSpeed end
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
players.PlayerAdded:Connect(function(p)
	p.CharacterAdded:Connect(function()
		task.wait(.3)
		if espOn then updateESP() end
	end)
end)
players.PlayerRemoving:Connect(function()
	if espOn then updateESP() end
end)
run.RenderStepped:Connect(function()
	if flying then
		local char=player.Character
		local root=char and char:FindFirstChild("HumanoidRootPart")
		if root then
			local bv=root:FindFirstChild("FlyVelocity")
			if not bv then
				bv=Instance.new("BodyVelocity")
				bv.Name="FlyVelocity"
				bv.MaxForce=Vector3.new(math.huge,math.huge,math.huge)
				bv.Parent=root
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
	end
	if aimOn then
		local target=nearestPlayer()
		if target then
			aimStatus.Text="Status: ON\nTarget: "..target.Name
		else
			aimStatus.Text="Status: ON\nTarget: NONE"
		end
	end
end)
task.spawn(function()
	while gui.Parent do
		if espOn then updateESP() end
		task.wait(.5)
	end
end)
task.spawn(function()
	while gui.Parent do
		local char=player.Character
		local hum=char and char:FindFirstChildOfClass("Humanoid")
		if hum then
			if speedOn and hum.WalkSpeed~=walkSpeed then hum.WalkSpeed=walkSpeed end
			if jumpOn and hum.JumpPower~=jumpPower then
				hum.UseJumpPower=true
				hum.JumpPower=jumpPower
			end
		end
		task.wait(.1)
	end
end)

local player=game.Players.LocalPlayer
local uis=game:GetService("UserInputService")
local run=game:GetService("RunService")
local players=game:GetService("Players")
local rs=game:GetService("ReplicatedStorage")
local flying=false
local speedOn=false
local jumpOn=false
local espOn=false
local autoAttackOn=false
local flySpeed=60
local walkSpeed=60
local jumpPower=50
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
frame.Size=UDim2.new(0,280,0,300)
frame.Position=UDim2.new(0.5,-140,0.5,-150)
frame.BackgroundColor3=Color3.fromRGB(20,20,25)
frame.Visible=false
frame.Parent=gui
local frameCorner=Instance.new("UICorner")
frameCorner.CornerRadius=UDim.new(0,15)
frameCorner.Parent=frame
local top=Instance.new("Frame")
top.Size=UDim2.new(1,0,0,50)
top.BackgroundColor3=Color3.fromRGB(28,28,34)
top.Parent=frame
local topCorner=Instance.new("UICorner")
topCorner.CornerRadius=UDim.new(0,15)
topCorner.Parent=top
local title=Instance.new("TextLabel")
title.Size=UDim2.new(1,-50,1,0)
title.Position=UDim2.new(0,10,0,0)
title.BackgroundTransparency=1
title.Text="VANH"
title.TextColor3=Color3.fromRGB(255,255,255)
title.TextSize=20
title.Font=Enum.Font.GothamBold
title.TextXAlignment=Enum.TextXAlignment.Left
title.Parent=top
local close=Instance.new("TextButton")
close.Size=UDim2.new(0,35,0,35)
close.Position=UDim2.new(1,-40,0,7.5)
close.BackgroundColor3=Color3.fromRGB(180,55,55)
close.Text="X"
close.TextColor3=Color3.fromRGB(255,255,255)
close.TextSize=16
close.Font=Enum.Font.GothamBold
close.Parent=top
local closeCorner=Instance.new("UICorner")
closeCorner.CornerRadius=UDim.new(0,9)
closeCorner.Parent=close
local st=Instance.new("Frame")
st.Size=UDim2.new(1,-20,1,-70)
st.Position=UDim2.new(0,10,0,60)
st.BackgroundTransparency=1
st.Parent=frame
local function mkBtn(text,y)
	local button=Instance.new("TextButton")
	button.Size=UDim2.new(0,80,0,30)
	button.Position=UDim2.new(0,0,0,y)
	button.BackgroundColor3=Color3.fromRGB(90,90,100)
	button.Text=text
	button.TextColor3=Color3.fromRGB(255,255,255)
	button.TextSize=10
	button.Font=Enum.Font.GothamBold
	button.Parent=st
	local corner=Instance.new("UICorner")
	corner.CornerRadius=UDim.new(0,9)
	corner.Parent=button
	return button
end
local function mkBox(value,y)
	local box=Instance.new("TextBox")
	box.Size=UDim2.new(0,120,0,30)
	box.Position=UDim2.new(0,95,0,y)
	box.BackgroundColor3=Color3.fromRGB(35,35,42)
	box.Text=tostring(value)
	box.TextColor3=Color3.fromRGB(255,255,255)
	box.TextSize=12
	box.Font=Enum.Font.Gotham
	box.ClearTextOnFocus=false
	box.Parent=st
	local corner=Instance.new("UICorner")
	corner.CornerRadius=UDim.new(0,9)
	corner.Parent=box
	return box
end
local flyButton=mkBtn("FLY OFF",0)
local flyBox=mkBox(flySpeed,0)
local speedButton=mkBtn("SPEED OFF",35)
local speedBox=mkBox(walkSpeed,35)
local jumpButton=mkBtn("JUMP OFF",70)
local jumpBox=mkBox(jumpPower,70)
local espFolder=Instance.new("Folder")
espFolder.Name="VanhESP"
espFolder.Parent=gui
local function clearESP()
	for _,obj in ipairs(espFolder:GetChildren()) do
		obj:Destroy()
	end
end
local function mkESP(target)
	if not espOn or target==player then return end
	local char=target.Character
	if not char then return end
	local humanoid=char:FindFirstChildOfClass("Humanoid")
	local head=char:FindFirstChild("Head")
	if not humanoid then return end
	local highlight=Instance.new("Highlight")
	highlight.Adornee=char
	highlight.FillTransparency=0.65
	highlight.OutlineTransparency=0
	highlight.Parent=espFolder
	if head then
		local billboard=Instance.new("BillboardGui")
		billboard.Adornee=head
		billboard.Size=UDim2.new(0,200,0,50)
		billboard.StudsOffset=Vector3.new(0,3,0)
		billboard.AlwaysOnTop=true
		billboard.Parent=espFolder
		local info=Instance.new("TextLabel")
		info.Size=UDim2.new(1,0,1,0)
		info.BackgroundTransparency=1
		info.TextColor3=Color3.fromRGB(255,255,255)
		info.TextStrokeTransparency=0
		info.TextSize=12
		info.Font=Enum.Font.GothamBold
		info.Text=target.DisplayName.."\nHP: "..math.floor(humanoid.Health).."/"..math.floor(humanoid.MaxHealth)
		info.Parent=billboard
	end
end
local function updateESP()
	clearESP()
	if not espOn then return end
	for _,target in ipairs(players:GetPlayers()) do mkESP(target) end
end
local function autoAttack()
	if not autoAttackOn then return end
	local char=player.Character
	local hrp=char and char:FindFirstChild("HumanoidRootPart")
	if not hrp then return end
	local enemies=workspace:FindFirstChild("Enemies") or workspace
	for _,enemy in ipairs(enemies:GetChildren()) do
		if enemy:IsA("Model") then
			local eHrp=enemy:FindFirstChild("HumanoidRootPart")
			local eHm=enemy:FindFirstChildOfClass("Humanoid")
			if eHrp and eHm and eHm.Health>0 and (eHrp.Position-hrp.Position).Magnitude<150 then
				hrp.CFrame=eHrp.CFrame+eHrp.CFrame.LookVector*3
				pcall(function()
					rs:WaitForChild("Remotes"):FindFirstChild("Attack"):FireServer()
				end)
				task.wait(0.2)
				return
			end
		end
	end
end
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
		local hrp=char and char:FindFirstChild("HumanoidRootPart")
		if hrp then
			local bv=hrp:FindFirstChild("FlyVelocity")
			if bv then bv:Destroy() end
		end
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
		if humanoid then humanoid.WalkSpeed=walkSpeed end
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
				if bv then bv:Destroy() end
			end)
		end
	else
		speedButton.Text="SPEED OFF"
		speedButton.BackgroundColor3=Color3.fromRGB(90,90,100)
		if humanoid then humanoid.WalkSpeed=16 end
		if hrp then
			local bv=hrp:FindFirstChild("SpeedVel")
			if bv then bv:Destroy() end
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
	local value=tonumber(flyBox.Text)
	if value and value>=1 then flySpeed=value else flyBox.Text=tostring(flySpeed) end
end)
speedBox.FocusLost:Connect(function()
	local value=tonumber(speedBox.Text)
	if value and value>=0 then
		walkSpeed=value
		if speedOn then
			local char=player.Character
			local humanoid=char and char:FindFirstChildOfClass("Humanoid")
			if humanoid then humanoid.WalkSpeed=walkSpeed end
		end
	else speedBox.Text=tostring(walkSpeed) end
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
	else jumpBox.Text=tostring(jumpPower) end
end)
local espButton=Instance.new("TextButton")
espButton.Size=UDim2.new(0,260,0,30)
espButton.Position=UDim2.new(0,10,1,-40)
espButton.BackgroundColor3=Color3.fromRGB(90,90,100)
espButton.Text="ESP OFF"
espButton.TextColor3=Color3.fromRGB(255,255,255)
espButton.TextSize=12
espButton.Font=Enum.Font.GothamBold
espButton.Parent=frame
local espCorner=Instance.new("UICorner")
espCorner.CornerRadius=UDim.new(0,9)
espCorner.Parent=espButton
local farmButton=Instance.new("TextButton")
farmButton.Size=UDim2.new(0,260,0,30)
farmButton.Position=UDim2.new(0,10,1,-70)
farmButton.BackgroundColor3=Color3.fromRGB(90,90,100)
farmButton.Text="FARM OFF"
farmButton.TextColor3=Color3.fromRGB(255,255,255)
farmButton.TextSize=12
farmButton.Font=Enum.Font.GothamBold
farmButton.Parent=frame
local farmCorner=Instance.new("UICorner")
farmCorner.CornerRadius=UDim.new(0,9)
farmCorner.Parent=farmButton
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
farmButton.MouseButton1Click:Connect(function()
	autoAttackOn=not autoAttackOn
	if autoAttackOn then
		farmButton.Text="FARM ON"
		farmButton.BackgroundColor3=Color3.fromRGB(45,170,90)
	else
		farmButton.Text="FARM OFF"
		farmButton.BackgroundColor3=Color3.fromRGB(90,90,100)
	end
end)
local confirm=Instance.new("Frame")
confirm.Size=UDim2.new(0,250,0,120)
confirm.Position=UDim2.new(0.5,-125,0.5,-60)
confirm.BackgroundColor3=Color3.fromRGB(25,25,30)
confirm.Visible=false
confirm.ZIndex=10
confirm.Parent=gui
local confirmCorner=Instance.new("UICorner")
confirmCorner.CornerRadius=UDim.new(0,13)
confirmCorner.Parent=confirm
local question=Instance.new("TextLabel")
question.Size=UDim2.new(1,-10,0,50)
question.Position=UDim2.new(0,5,0,5)
question.BackgroundTransparency=1
question.Text="Tắt script?"
question.TextColor3=Color3.fromRGB(255,255,255)
question.TextSize=16
question.Font=Enum.Font.GothamBold
question.ZIndex=11
question.Parent=confirm
local yes=Instance.new("TextButton")
yes.Size=UDim2.new(0,100,0,35)
yes.Position=UDim2.new(0,15,0,70)
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
no.Size=UDim2.new(0,100,0,35)
no.Position=UDim2.new(0,135,0,70)
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
	autoAttackOn=false
	clearESP()
	local char=player.Character
	local hrp=char and char:FindFirstChild("HumanoidRootPart")
	if hrp then
		local bv=hrp:FindFirstChild("FlyVelocity")
		if bv then bv:Destroy() end
		local sv=hrp:FindFirstChild("SpeedVel")
		if sv then sv:Destroy() end
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
	if input.UserInputType==Enum.UserInputType.MouseButton1 then dragging=false end
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
	if input.UserInputType==Enum.UserInputType.MouseButton1 then openDragging=false end
end)
uis.InputChanged:Connect(function(input)
	if openDragging and input.UserInputType==Enum.UserInputType.MouseMovement then
		local delta=input.Position-openDragStart
		open.Position=UDim2.new(openStartPos.X.Scale,openStartPos.X.Offset+delta.X,openStartPos.Y.Scale,openStartPos.Y.Offset+delta.Y)
	end
end)
player.CharacterAdded:Connect(function(char)
	local humanoid=char:WaitForChild("Humanoid")
	if speedOn then humanoid.WalkSpeed=walkSpeed end
	if jumpOn then
		humanoid.UseJumpPower=true
		humanoid.JumpPower=jumpPower
	end
end)
players.PlayerAdded:Connect(function(target)
	target.CharacterAdded:Connect(function()
		task.wait(0.3)
		if espOn then updateESP() end
	end)
end)
players.PlayerRemoving:Connect(function()
	if espOn then updateESP() end
end)
connection=run.RenderStepped:Connect(function()
	if not flying then return end
	local char=player.Character
	local hrp=char and char:FindFirstChild("HumanoidRootPart")
	if not hrp then return end
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
	if move.Magnitude>0 then
		bv.Velocity=move.Unit*flySpeed
	else
		bv.Velocity=Vector3.zero
	end
end)
task.spawn(function()
	while gui.Parent do
		if espOn then updateESP() end
		task.wait(0.35)
	end
end)
task.spawn(function()
	while gui.Parent do
		local char=player.Character
		local humanoid=char and char:FindFirstChildOfClass("Humanoid")
		if humanoid then
			if speedOn and humanoid.WalkSpeed~=walkSpeed then humanoid.WalkSpeed=walkSpeed end
			if jumpOn and humanoid.JumpPower~=jumpPower then
				humanoid.UseJumpPower=true
				humanoid.JumpPower=jumpPower
			end
		end
		task.wait(0.1)
	end
end)
task.spawn(function()
	while gui.Parent do
		autoAttack()
		task.wait(0.1)
	end
end)

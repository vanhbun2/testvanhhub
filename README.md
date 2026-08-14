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
local farmHeight=7
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
open.TextColor3=Color3.new(1,1,1)
open.TextSize=18
open.Font=Enum.Font.GothamBold
open.Parent=gui
Instance.new("UICorner",open).CornerRadius=UDim.new(0,10)

local frame=Instance.new("Frame")
frame.Size=UDim2.new(0,330,0,410)
frame.Position=UDim2.new(.5,-165,.5,-205)
frame.BackgroundColor3=Color3.fromRGB(20,20,25)
frame.Visible=false
frame.Parent=gui
Instance.new("UICorner",frame).CornerRadius=UDim.new(0,15)

local top=Instance.new("Frame")
top.Size=UDim2.new(1,0,0,55)
top.BackgroundColor3=Color3.fromRGB(28,28,34)
top.Parent=frame
Instance.new("UICorner",top).CornerRadius=UDim.new(0,15)

local title=Instance.new("TextLabel")
title.Size=UDim2.new(1,-100,1,0)
title.Position=UDim2.new(0,18,0,0)
title.BackgroundTransparency=1
title.Text="VANH"
title.TextColor3=Color3.new(1,1,1)
title.TextSize=22
title.Font=Enum.Font.GothamBold
title.TextXAlignment=Enum.TextXAlignment.Left
title.Parent=top

local close=Instance.new("TextButton")
close.Size=UDim2.new(0,35,0,35)
close.Position=UDim2.new(1,-45,0,10)
close.BackgroundColor3=Color3.fromRGB(180,55,55)
close.Text="X"
close.TextColor3=Color3.new(1,1,1)
close.TextSize=16
close.Font=Enum.Font.GothamBold
close.Parent=top
Instance.new("UICorner",close).CornerRadius=UDim.new(0,9)

local settings=Instance.new("TextButton")
settings.Size=UDim2.new(0,140,0,38)
settings.Position=UDim2.new(0,20,0,70)
settings.BackgroundColor3=Color3.fromRGB(55,55,65)
settings.Text="SETTINGS"
settings.TextColor3=Color3.new(1,1,1)
settings.TextSize=14
settings.Font=Enum.Font.GothamBold
settings.Parent=frame
Instance.new("UICorner",settings).CornerRadius=UDim.new(0,9)

local farm=Instance.new("TextButton")
farm.Size=UDim2.new(0,140,0,38)
farm.Position=UDim2.new(0,170,0,70)
farm.BackgroundColor3=Color3.fromRGB(35,35,42)
farm.Text="FARM"
farm.TextColor3=Color3.fromRGB(180,180,190)
farm.TextSize=14
farm.Font=Enum.Font.GothamBold
farm.Parent=frame
Instance.new("UICorner",farm).CornerRadius=UDim.new(0,9)

local settingsPage=Instance.new("Frame")
settingsPage.Size=UDim2.new(1,-40,1,-125)
settingsPage.Position=UDim2.new(0,20,0,120)
settingsPage.BackgroundTransparency=1
settingsPage.Parent=frame

local farmPage=Instance.new("Frame")
farmPage.Size=UDim2.new(1,-40,1,-125)
farmPage.Position=UDim2.new(0,20,0,120)
farmPage.BackgroundTransparency=1
farmPage.Visible=false
farmPage.Parent=frame

local function button(text,y)
	local b=Instance.new("TextButton")
	b.Size=UDim2.new(0,100,0,38)
	b.Position=UDim2.new(0,0,0,y)
	b.BackgroundColor3=Color3.fromRGB(90,90,100)
	b.Text=text
	b.TextColor3=Color3.new(1,1,1)
	b.TextSize=12
	b.Font=Enum.Font.GothamBold
	b.Parent=settingsPage
	Instance.new("UICorner",b).CornerRadius=UDim.new(0,9)
	return b
end

local function box(value,y)
	local b=Instance.new("TextBox")
	b.Size=UDim2.new(0,165,0,38)
	b.Position=UDim2.new(0,125,0,y)
	b.BackgroundColor3=Color3.fromRGB(35,35,42)
	b.Text=tostring(value)
	b.TextColor3=Color3.new(1,1,1)
	b.TextSize=14
	b.Font=Enum.Font.Gotham
	b.ClearTextOnFocus=false
	b.Parent=settingsPage
	Instance.new("UICorner",b).CornerRadius=UDim.new(0,9)
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
	for _,v in ipairs(espFolder:GetChildren()) do v:Destroy() end
end

local function makeESP(p)
	if not espOn or p==player then return end
	local c=p.Character
	if not c then return end
	local h=c:FindFirstChildOfClass("Humanoid")
	local head=c:FindFirstChild("Head")
	if not h then return end
	local hi=Instance.new("Highlight")
	hi.Adornee=c
	hi.FillTransparency=.65
	hi.Parent=espFolder
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
		t.TextColor3=Color3.new(1,1,1)
		t.TextStrokeTransparency=0
		t.TextSize=14
		t.Font=Enum.Font.GothamBold
		t.Text=p.DisplayName.." ["..p.Name.."]\nHP: "..math.floor(h.Health).."/"..math.floor(h.MaxHealth)
		t.Parent=bb
	end
end

local function updateESP()
	clearESP()
	if espOn then
		for _,p in ipairs(players:GetPlayers()) do
			makeESP(p)
		end
	end
end

local function getPlayerLevel()
	local c=player.Character
	if not c then return 0 end
	local text=""
	for _,v in ipairs(c:GetDescendants()) do
		if v:IsA("TextLabel") or v:IsA("TextButton") then
			text=text.." "..v.Text
		end
	end
	local n=tonumber(text:match("[Ll][Vv][Ee][Ll]%s*[:%-]?%s*(%d+)"))
	if n then return n end
	n=tonumber(text:match("[Ll][Vv]%s*[:%-]?%s*(%d+)"))
	return n or 0
end

local function getEnemyLevel(enemy)
	local text=""
	for _,v in ipairs(enemy:GetDescendants()) do
		if v:IsA("TextLabel") or v:IsA("TextButton") then
			text=text.." "..v.Text
		end
	end
	for _,v in ipairs(enemy:GetChildren()) do
		if v:IsA("StringValue") or v:IsA("IntValue") or v:IsA("NumberValue") then
			if string.find(string.lower(v.Name),"level") or string.find(string.lower(v.Name),"lvl") then
				return tonumber(v.Value) or 0
			end
		end
	end
	local n=tonumber(text:match("[Ll][Vv][Ee][Ll]%s*[:%-]?%s*(%d+)"))
	if n then return n end
	n=tonumber(text:match("[Ll][Vv]%s*[:%-]?%s*(%d+)"))
	if n then return n end
	return tonumber(text:match("%[(%d+)%]")) or 0
end

local function getTarget()
	local c=player.Character
	local hrp=c and c:FindFirstChild("HumanoidRootPart")
	if not hrp then return nil end
	local lvl=getPlayerLevel()
	if lvl<=0 then return nil end
	local folder=workspace:FindFirstChild("Enemies")
	if not folder then return nil end
	local target=nil
	local distance=math.huge
	for _,enemy in ipairs(folder:GetChildren()) do
		local eHrp=enemy:FindFirstChild("HumanoidRootPart")
		local hum=enemy:FindFirstChildOfClass("Humanoid")
		if eHrp and hum and hum.Health>0 then
			local elvl=getEnemyLevel(enemy)
			if elvl>0 and elvl<=lvl then
				local d=(eHrp.Position-hrp.Position).Magnitude
				if d<distance then
					distance=d
					target=enemy
				end
			end
		end
	end
	return target
end

local function farmEnemy()
	if not autoAttackOn then return end
	local c=player.Character
	local hrp=c and c:FindFirstChild("HumanoidRootPart")
	if not hrp then return end
	local enemy=getTarget()
	if not enemy then
		farmStatus.Text="Status: NO TARGET"
		return
	end
	local eHrp=enemy:FindFirstChild("HumanoidRootPart")
	local hum=enemy:FindFirstChildOfClass("Humanoid")
	if not eHrp or not hum or hum.Health<=0 then return end
	hrp.CFrame=CFrame.new(
		eHrp.Position+Vector3.new(0,farmHeight,0),
		eHrp.Position
	)
	farmStatus.Text="Status: FARMING\nTarget: "..enemy.Name.."\nLevel: "..getEnemyLevel(enemy).."/"..getPlayerLevel()
	pcall(function()
		local remotes=rs:FindFirstChild("Remotes")
		local attack=remotes and remotes:FindFirstChild("Attack")
		if attack then
			attack:FireServer()
		end
	end)
end

local farmTitle=Instance.new("TextLabel")
farmTitle.Size=UDim2.new(1,0,0,30)
farmTitle.Position=UDim2.new(0,0,0,5)
farmTitle.BackgroundTransparency=1
farmTitle.Text="AUTO FARM • NPC"
farmTitle.TextColor3=Color3.fromRGB(150,150,160)
farmTitle.TextSize=16
farmTitle.Font=Enum.Font.GothamBold
farmTitle.Parent=farmPage

local farmStatus=Instance.new("TextLabel")
farmStatus.Size=UDim2.new(0,290,0,100)
farmStatus.Position=UDim2.new(0,0,0,45)
farmStatus.BackgroundTransparency=1
farmStatus.Text="Status: OFF"
farmStatus.TextColor3=Color3.fromRGB(180,180,190)
farmStatus.TextSize=12
farmStatus.Font=Enum.Font.Gotham
farmStatus.TextWrapped=true
farmStatus.TextYAlignment=Enum.TextYAlignment.Top
farmStatus.Parent=farmPage

local farmButton=Instance.new("TextButton")
farmButton.Size=UDim2.new(0,290,0,40)
farmButton.Position=UDim2.new(0,0,0,155)
farmButton.BackgroundColor3=Color3.fromRGB(90,90,100)
farmButton.Text="FARM OFF"
farmButton.TextColor3=Color3.new(1,1,1)
farmButton.TextSize=14
farmButton.Font=Enum.Font.GothamBold
farmButton.Parent=farmPage
Instance.new("UICorner",farmButton).CornerRadius=UDim.new(0,9)

farmButton.MouseButton1Click:Connect(function()
	autoAttackOn=not autoAttackOn
	if autoAttackOn then
		farmButton.Text="FARM ON"
		farmButton.BackgroundColor3=Color3.fromRGB(45,170,90)
		farmStatus.Text="Status: FARMING"
	else
		farmButton.Text="FARM OFF"
		farmButton.BackgroundColor3=Color3.fromRGB(90,90,100)
		farmStatus.Text="Status: OFF"
	end
end)

open.MouseButton1Click:Connect(function()
	frame.Visible=not frame.Visible
end)

settings.MouseButton1Click:Connect(function()
	settingsPage.Visible=true
	farmPage.Visible=false
	settings.BackgroundColor3=Color3.fromRGB(55,55,65)
	farm.BackgroundColor3=Color3.fromRGB(35,35,42)
end)

farm.MouseButton1Click:Connect(function()
	settingsPage.Visible=false
	farmPage.Visible=true
	settings.BackgroundColor3=Color3.fromRGB(35,35,42)
	farm.BackgroundColor3=Color3.fromRGB(55,55,65)
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
		local c=player.Character
		local h=c and c:FindFirstChild("HumanoidRootPart")
		local bv=h and h:FindFirstChild("FlyVelocity")
		if bv then bv:Destroy() end
	end
end)

speedButton.MouseButton1Click:Connect(function()
	speedOn=not speedOn
	local c=player.Character
	local h=c and c:FindFirstChildOfClass("Humanoid")
	if speedOn then
		speedButton.Text="SPEED ON"
		speedButton.BackgroundColor3=Color3.fromRGB(45,170,90)
		if h then h.WalkSpeed=walkSpeed end
	else
		speedButton.Text="SPEED OFF"
		speedButton.BackgroundColor3=Color3.fromRGB(90,90,100)
		if h then h.WalkSpeed=16 end
	end
end)

jumpButton.MouseButton1Click:Connect(function()
	jumpOn=not jumpOn
	local c=player.Character
	local h=c and c:FindFirstChildOfClass("Humanoid")
	if jumpOn then
		jumpButton.Text="JUMP ON"
		jumpButton.BackgroundColor3=Color3.fromRGB(45,170,90)
		if h then
			h.UseJumpPower=true
			h.JumpPower=jumpPower
		end
	else
		jumpButton.Text="JUMP OFF"
		jumpButton.BackgroundColor3=Color3.fromRGB(90,90,100)
		if h then h.JumpPower=50 end
	end
end)

flyBox.FocusLost:Connect(function()
	local v=tonumber(flyBox.Text)
	if v and v>=1 then flySpeed=v else flyBox.Text=tostring(flySpeed) end
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

local confirm=Instance.new("Frame")
confirm.Size=UDim2.new(0,280,0,145)
confirm.Position=UDim2.new(.5,-140,.5,-72)
confirm.BackgroundColor3=Color3.fromRGB(25,25,30)
confirm.Visible=false
confirm.ZIndex=10
confirm.Parent=gui
Instance.new("UICorner",confirm).CornerRadius=UDim.new(0,13)

local question=Instance.new("TextLabel")
question.Size=UDim2.new(1,-20,0,65)
question.Position=UDim2.new(0,10,0,10)
question.BackgroundTransparency=1
question.Text="Bạn có xác định tắt script?"
question.TextColor3=Color3.new(1,1,1)
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
yes.TextColor3=Color3.new(1,1,1)
yes.TextSize=14
yes.Font=Enum.Font.GothamBold
yes.ZIndex=11
yes.Parent=confirm
Instance.new("UICorner",yes).CornerRadius=UDim.new(0,8)

local no=Instance.new("TextButton")
no.Size=UDim2.new(0,105,0,38)
no.Position=UDim2.new(0,150,0,90)
no.BackgroundColor3=Color3.fromRGB(60,60,70)
no.Text="KHÔNG"
no.TextColor3=Color3.new(1,1,1)
no.TextSize=14
no.Font=Enum.Font.GothamBold
no.ZIndex=11
no.Parent=confirm
Instance.new("UICorner",no).CornerRadius=UDim.new(0,8)

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
	if connection then
		connection:Disconnect()
		connection=nil
	end
	gui:Destroy()
end)

local dragging=false
local dragStart
local startPos

top.InputBegan:Connect(function(i)
	if i.UserInputType==Enum.UserInputType.MouseButton1 then
		dragging=true
		dragStart=i.Position
		startPos=frame.Position
	end
end)

top.InputEnded:Connect(function(i)
	if i.UserInputType==Enum.UserInputType.MouseButton1 then
		dragging=false
	end
end)

uis.InputChanged:Connect(function(i)
	if dragging and i.UserInputType==Enum.UserInputType.MouseMovement then
		local d=i.Position-dragStart
		frame.Position=UDim2.new(startPos.X.Scale,startPos.X.Offset+d.X,startPos.Y.Scale,startPos.Y.Offset+d.Y)
	end
end)

player.CharacterAdded:Connect(function(c)
	local h=c:WaitForChild("Humanoid")
	if speedOn then h.WalkSpeed=walkSpeed end
	if jumpOn then
		h.UseJumpPower=true
		h.JumpPower=jumpPower
	end
end)

connection=run.RenderStepped:Connect(function()
	if not flying then return end
	local c=player.Character
	local hrp=c and c:FindFirstChild("HumanoidRootPart")
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
	if uis:IsKeyDown(Enum.KeyCode.Space) then move+=Vector3.yAxis end
	if uis:IsKeyDown(Enum.KeyCode.LeftControl) then move-=Vector3.yAxis end
	bv.Velocity=move.Magnitude>0 and move.Unit*flySpeed or Vector3.zero
end)

task.spawn(function()
	while gui.Parent do
		if autoAttackOn then
			pcall(farmEnemy)
		end
		task.wait(.15)
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
		local c=player.Character
		local h=c and c:FindFirstChildOfClass("Humanoid")
		if h then
			if speedOn then h.WalkSpeed=walkSpeed end
			if jumpOn then
				h.UseJumpPower=true
				h.JumpPower=jumpPower
			end
		end
		task.wait(.1)
	end
end)

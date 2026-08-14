local player=game.Players.LocalPlayer
local uis=game:GetService("UserInputService")
local run=game:GetService("RunService")
local players=game:GetService("Players")
local mouse=player:GetMouse()
local state={flying=false,speedOn=false,farmOn=false,espOn=false}
local cfg={flySpeed=60,walkSpeed=35,espRate=0.35}
local cons={}
local gui,espFolder
local function con(event,fn)
	table.insert(cons,event:Connect(fn))
end
local function cor(fn)
	table.insert(cons,task.spawn(fn))
end
local function mkBtn(txt,sz,pos,bg,p)
	local b=Instance.new("TextButton")
	b.Size,b.Position=sz,pos
	b.BackgroundColor3=bg
	b.Text=txt
	b.TextColor3=Color3.fromRGB(255,255,255)
	b.TextSize=13
	b.Font=Enum.Font.GothamBold
	b.Parent=p
	local cr=Instance.new("UICorner")
	cr.CornerRadius=UDim.new(0,8)
	cr.Parent=b
	return b
end
local function mkBox(val,sz,pos,p)
	local bx=Instance.new("TextBox")
	bx.Size,bx.Position=sz,pos
	bx.BackgroundColor3=Color3.fromRGB(35,35,42)
	bx.Text=tostring(val)
	bx.TextColor3=Color3.fromRGB(255,255,255)
	bx.TextSize=13
	bx.Font=Enum.Font.Gotham
	bx.ClearTextOnFocus=false
	bx.Parent=p
	local cr=Instance.new("UICorner")
	cr.CornerRadius=UDim.new(0,8)
	cr.Parent=bx
	return bx
end
local function mkLbl(txt,sz,pos,p,col,tsz)
	local l=Instance.new("TextLabel")
	l.Size,l.Position=sz,pos
	l.BackgroundTransparency=1
	l.Text=txt
	l.TextColor3=col or Color3.fromRGB(150,150,160)
	l.TextSize=tsz or 11
	l.Font=Enum.Font.Gotham
	l.Parent=p
	return l
end
local function updBtn(btn,on)
	btn.Text=btn.Tag.." "..(on and "ON" or "OFF")
	btn.BackgroundColor3=on and Color3.fromRGB(55,155,90) or Color3.fromRGB(90,90,100)
end
local function farmLogic()
	local farm=workspace:FindFirstChild("NPCSpawner") or workspace:FindFirstChild("Enemies") or workspace
	if not farm then return end
	local char=player.Character
	local hrp=char and char:FindFirstChild("HumanoidRootPart")
	if not hrp then return end
	for _,npc in ipairs(farm:GetChildren()) do
		if npc:FindFirstChild("Humanoid") then
			local npcHrp=npc:FindFirstChild("HumanoidRootPart")
			if npcHrp and (npcHrp.Position-hrp.Position).Magnitude<50 then
				hrp.CFrame=npcHrp.CFrame+npcHrp.CFrame.LookVector*3
				pcall(function()
					game:GetService("ReplicatedStorage"):WaitForChild("Remotes"):FindFirstChild("Attack"):FireServer()
				end)
				task.wait(0.5)
			end
		end
	end
end
local function flyLoop()
	local fc
	fc=run.RenderStepped:Connect(function()
		if not state.flying then fc:Disconnect() return end
		local char=player.Character
		local hrp=char and char:FindFirstChild("HumanoidRootPart")
		if not hrp then return end
		local bv=hrp:FindFirstChild("FlyVel")
		if not bv then
			bv=Instance.new("BodyVelocity")
			bv.Name="FlyVel"
			bv.MaxForce=Vector3.new(math.huge,math.huge,math.huge)
			bv.Parent=hrp
		end
		local cam=workspace.CurrentCamera
		local mov=Vector3.zero
		if uis:IsKeyDown(Enum.KeyCode.W) then mov+=cam.CFrame.LookVector end
		if uis:IsKeyDown(Enum.KeyCode.S) then mov-=cam.CFrame.LookVector end
		if uis:IsKeyDown(Enum.KeyCode.A) then mov-=cam.CFrame.RightVector end
		if uis:IsKeyDown(Enum.KeyCode.D) then mov+=cam.CFrame.RightVector end
		if uis:IsKeyDown(Enum.KeyCode.Space) then mov+=Vector3.new(0,1,0) end
		if uis:IsKeyDown(Enum.KeyCode.LeftControl) then mov-=Vector3.new(0,1,0) end
		bv.Velocity=mov.Magnitude>0 and mov.Unit*cfg.flySpeed or Vector3.zero
	end)
	table.insert(cons,fc)
end
local function stopFly()
	local char=player.Character
	local hrp=char and char:FindFirstChild("HumanoidRootPart")
	if hrp then
		local bv=hrp:FindFirstChild("FlyVel")
		if bv then bv:Destroy() end
	end
end
local function clearESP()
	if espFolder then
		for _,o in ipairs(espFolder:GetChildren()) do
			pcall(function() o:Destroy() end)
		end
	end
end
local function mkESP(tgt)
	if not state.espOn or tgt==player then return end
	local ch=tgt.Character
	if not ch then return end
	local hm=ch:FindFirstChildOfClass("Humanoid")
	local hd=ch:FindFirstChild("Head")
	if not hm then return end
	local hl=Instance.new("Highlight")
	hl.Adornee=ch
	hl.FillTransparency=0.65
	hl.OutlineTransparency=0
	hl.Parent=espFolder
	if hd then
		local bb=Instance.new("BillboardGui")
		bb.Adornee=hd
		bb.Size=UDim2.new(0,250,0,55)
		bb.StudsOffset=Vector3.new(0,3,0)
		bb.AlwaysOnTop=true
		bb.Parent=espFolder
		local inf=Instance.new("TextLabel")
		inf.Size=UDim2.new(1,0,1,0)
		inf.BackgroundTransparency=1
		inf.TextColor3=Color3.fromRGB(255,255,255)
		inf.TextStrokeTransparency=0
		inf.TextSize=14
		inf.Font=Enum.Font.GothamBold
		inf.Text=tgt.DisplayName.." ["..tgt.Name.."]\nHP: "..math.floor(hm.Health).."/"..math.floor(hm.MaxHealth)
		inf.Parent=bb
	end
end
local function updESP()
	clearESP()
	if not state.espOn then return end
	for _,t in ipairs(players:GetPlayers()) do mkESP(t) end
end
local function mkGUI()
	gui=Instance.new("ScreenGui")
	gui.Name="VanhBlox"
	gui.ResetOnSpawn=false
	gui.Parent=player:WaitForChild("PlayerGui")
	espFolder=Instance.new("Folder")
	espFolder.Name="VanhESP"
	espFolder.Parent=gui
	local opn=Instance.new("TextButton")
	opn.Size=UDim2.new(0,90,0,42)
	opn.Position=UDim2.new(0,20,0.5,-21)
	opn.BackgroundColor3=Color3.fromRGB(25,25,30)
	opn.Text="VANH BLOX"
	opn.TextColor3=Color3.fromRGB(255,255,255)
	opn.TextSize=12
	opn.Font=Enum.Font.GothamBold
	opn.Parent=gui
	local crn=Instance.new("UICorner")
	crn.CornerRadius=UDim.new(0,10)
	crn.Parent=opn
	local fr=Instance.new("Frame")
	fr.Size=UDim2.new(0,360,0,450)
	fr.Position=UDim2.new(0.5,-180,0.5,-225)
	fr.BackgroundColor3=Color3.fromRGB(18,18,24)
	fr.Visible=false
	fr.ZIndex=5
	fr.Parent=gui
	local frCrn=Instance.new("UICorner")
	frCrn.CornerRadius=UDim.new(0,15)
	frCrn.Parent=fr
	local top=Instance.new("Frame")
	top.Size=UDim2.new(1,0,0,60)
	top.BackgroundColor3=Color3.fromRGB(28,28,38)
	top.Parent=fr
	top.ZIndex=6
	local topCrn=Instance.new("UICorner")
	topCrn.CornerRadius=UDim.new(0,15)
	topCrn.Parent=top
	local titleLbl=mkLbl("VANH • BLOX FRUIT",UDim2.new(1,-100,1,0),UDim2.new(0,18,0,0),top,Color3.fromRGB(255,255,255),20)
	titleLbl.ZIndex=6
	local cls=mkBtn("X",UDim2.new(0,35,0,35),UDim2.new(1,-45,0,12),Color3.fromRGB(200,60,60),top)
	cls.ZIndex=7
	local st=Instance.new("Frame")
	st.Size=UDim2.new(1,-40,1,-120)
	st.Position=UDim2.new(0,20,0,75)
	st.BackgroundTransparency=1
	st.ZIndex=6
	st.Parent=fr
	local flyB=mkBtn("FLY OFF",UDim2.new(0,110,0,40),UDim2.new(0,0,0,0),Color3.fromRGB(90,90,100),st)
	flyB.Tag="FLY"
	flyB.ZIndex=6
	local flyBx=mkBox(cfg.flySpeed,UDim2.new(0,200,0,40),UDim2.new(0,125,0,0),st)
	flyBx.ZIndex=6
	local spd=mkBtn("SPEED OFF",UDim2.new(0,110,0,40),UDim2.new(0,0,0,55),Color3.fromRGB(90,90,100),st)
	spd.Tag="SPEED"
	spd.ZIndex=6
	local spdBx=mkBox(cfg.walkSpeed,UDim2.new(0,200,0,40),UDim2.new(0,125,0,55),st)
	spdBx.ZIndex=6
	local farm=mkBtn("FARM OFF",UDim2.new(0,110,0,40),UDim2.new(0,0,0,110),Color3.fromRGB(90,90,100),st)
	farm.Tag="FARM"
	farm.ZIndex=6
	local esp=mkBtn("ESP OFF",UDim2.new(0,110,0,40),UDim2.new(0,0,0,165),Color3.fromRGB(90,90,100),st)
	esp.Tag="ESP"
	esp.ZIndex=6
	local stSts=mkLbl("Ready",UDim2.new(0,200,0,25),UDim2.new(0,125,0,165),st,Color3.fromRGB(100,200,100),11)
	stSts.ZIndex=6
	con(flyB.MouseButton1Click,function()
		state.flying=not state.flying
		updBtn(flyB,state.flying)
		if state.flying then flyLoop() else stopFly() end
	end)
	con(spd.MouseButton1Click,function()
		state.speedOn=not state.speedOn
		updBtn(spd,state.speedOn)
		local ch=player.Character
		local hm=ch and ch:FindFirstChildOfClass("Humanoid")
		if hm then
			hm.WalkSpeed=state.speedOn and cfg.walkSpeed or 16
		end
	end)
	con(farm.MouseButton1Click,function()
		state.farmOn=not state.farmOn
		updBtn(farm,state.farmOn)
		stSts.Text=state.farmOn and "Farming..." or "Ready"
		stSts.TextColor3=state.farmOn and Color3.fromRGB(100,200,100) or Color3.fromRGB(150,150,160)
	end)
	con(esp.MouseButton1Click,function()
		state.espOn=not state.espOn
		updBtn(esp,state.espOn)
		if state.espOn then updESP() else clearESP() end
	end)
	con(flyBx.FocusLost,function()
		local v=tonumber(flyBx.Text)
		cfg.flySpeed=v and v>=1 and v or cfg.flySpeed
		flyBx.Text=tostring(cfg.flySpeed)
	end)
	con(spdBx.FocusLost,function()
		local v=tonumber(spdBx.Text)
		cfg.walkSpeed=v and v>=0 and v or cfg.walkSpeed
		spdBx.Text=tostring(cfg.walkSpeed)
		if state.speedOn then
			local ch=player.Character
			local hm=ch and ch:FindFirstChildOfClass("Humanoid")
			if hm then hm.WalkSpeed=cfg.walkSpeed end
		end
	end)
	con(opn.MouseButton1Click,function()
		fr.Visible=not fr.Visible
	end)
	local dragF,dragS,posS=false,nil,nil
	con(top.InputBegan,function(inp)
		if inp.UserInputType==Enum.UserInputType.MouseButton1 then
			dragF=true
			dragS=inp.Position
			posS=fr.Position
		end
	end)
	con(top.InputEnded,function(inp)
		if inp.UserInputType==Enum.UserInputType.MouseButton1 then dragF=false end
	end)
	con(uis.InputChanged,function(inp)
		if dragF and inp.UserInputType==Enum.UserInputType.MouseMovement then
			local dlt=inp.Position-dragS
			fr.Position=UDim2.new(posS.X.Scale,posS.X.Offset+dlt.X,posS.Y.Scale,posS.Y.Offset+dlt.Y)
		end
	end)
	local dragO,dragOS,posO=false,nil,nil
	con(opn.MouseButton1Down,function()
		dragO=true
		dragOS={x=mouse.X,y=mouse.Y}
		posO=opn.Position
	end)
	con(opn.MouseButton1Up,function()
		dragO=false
	end)
	con(uis.InputChanged,function(inp)
		if dragO and inp.UserInputType==Enum.UserInputType.MouseMovement then
			local dlt={x=mouse.X-dragOS.x,y=mouse.Y-dragOS.y}
			opn.Position=UDim2.new(posO.X.Scale,posO.X.Offset+dlt.x,posO.Y.Scale,posO.Y.Offset+dlt.y)
		end
	end)
	con(cls.MouseButton1Click,function()
		state.flying,state.speedOn,state.farmOn,state.espOn=false,false,false,false
		stopFly()
		clearESP()
		for _,c in ipairs(cons) do
			pcall(function() c:Disconnect() end)
		end
		gui:Destroy()
	end)
	cor(function()
		while gui and gui.Parent do
			if state.espOn then updESP() end
			task.wait(cfg.espRate)
		end
	end)
	cor(function()
		while gui and gui.Parent do
			if state.farmOn then farmLogic() end
			task.wait(0.2)
		end
	end)
	con(player.CharacterAdded,function(ch)
		local hm=ch:WaitForChild("Humanoid")
		if state.speedOn then hm.WalkSpeed=cfg.walkSpeed end
	end)
	con(players.PlayerAdded,function(tgt)
		tgt.CharacterAdded:Connect(function()
			task.wait(0.3)
			if state.espOn then updESP() end
		end)
	end)
	con(players.PlayerRemoving,function()
		if state.espOn then updESP() end
	end)
end
mkGUI()

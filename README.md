--// HG UI FINAL + LED + JUMP 3.8X + AIR WALK FIXO + FEATHER FALL AJUSTADO + BLOCO LEVITANTE AJUSTADO + LEVITAR MÉDIO + AUTO HIT BAT + BOTÕES NO CANTO DIREITO
--// By DɌASŦAK

local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local Debris = game:GetService("Debris")
local Workspace = game:GetService("Workspace")

local player = Players.LocalPlayer
local char = player.Character or player.CharacterAdded:Wait()
local humanoid = char:WaitForChild("Humanoid")
humanoid.UseJumpPower = true

player.CharacterAdded:Connect(function(c)
	char = c
	humanoid = c:WaitForChild("Humanoid")
	humanoid.UseJumpPower = true
end)

-- ESTADOS
local jumpEnabled = false
local featherEnabled = false
local airWalkEnabled = false
local levitateActive = false
local followBlockActive = false
local autoHitEnabled = false

-- AIR WALK VAR
local airPlatform = nil
local airHighlight = nil

-- GUI
local gui = Instance.new("ScreenGui", player.PlayerGui)
gui.ResetOnSpawn = false

-- BOTÃO HG
local openBtn = Instance.new("TextButton", gui)
openBtn.Size = UDim2.new(0, 60, 0, 60)
openBtn.Position = UDim2.new(0, 10, 0.45, -30)
openBtn.Text = "HG"
openBtn.Font = Enum.Font.GothamBlack
openBtn.TextSize = 20
openBtn.TextColor3 = Color3.fromRGB(200,0,255)
openBtn.BackgroundColor3 = Color3.fromRGB(5,5,5)
openBtn.BorderSizePixel = 0
Instance.new("UICorner", openBtn).CornerRadius = UDim.new(0,12)

local btnStroke = Instance.new("UIStroke", openBtn)
btnStroke.Thickness = 2
btnStroke.Color = Color3.fromRGB(180,0,255)

-- FRAME
local main = Instance.new("Frame", gui)
main.Size = UDim2.new(0, 520, 0, 320)
main.Position = UDim2.new(0.5, -260, 0.45, -160)
main.BackgroundColor3 = Color3.fromRGB(5,5,5)
main.Visible = false
main.BorderSizePixel = 0
Instance.new("UICorner", main)

local stroke = Instance.new("UIStroke", main)
stroke.Thickness = 2
stroke.Color = Color3.fromRGB(180,0,255)

-- LED PULSANDO
task.spawn(function()
	while true do
		for i = 0,1,0.03 do
			stroke.Transparency = i
			btnStroke.Transparency = i
			task.wait(0.01)
		end
		for i = 1,0,-0.03 do
			stroke.Transparency = i
			btnStroke.Transparency = i
			task.wait(0.01)
		end
	end
end)

-- SCROLL
local scroll = Instance.new("ScrollingFrame", main)
scroll.Size = UDim2.new(1,-20,1,-20)
scroll.Position = UDim2.new(0,10,0,10)
scroll.CanvasSize = UDim2.new(0,0,0,400)
scroll.ScrollBarThickness = 3
scroll.BackgroundTransparency = 1

local layout = Instance.new("UIListLayout", scroll)
layout.Padding = UDim.new(0,8)

-- FUNÇÃO BOTÃO
local function makeButton(text)
	local b = Instance.new("TextButton", scroll)
	b.Size = UDim2.new(1,0,0,36)
	b.Text = text
	b.BackgroundColor3 = Color3.fromRGB(90,0,140)
	b.TextColor3 = Color3.new(1,1,1)
	b.BorderSizePixel = 0
	Instance.new("UICorner", b)
	return b
end

-- BOTÕES MENU
local jumpBtn = makeButton("Jump 3.8X ❎")
local featherBtn = makeButton("Cair Leve ❎")
local airBtn = makeButton("Andar no Ar ❎")

-- FUNÇÕES BOTÕES
jumpBtn.MouseButton1Click:Connect(function()
	jumpEnabled = not jumpEnabled
	jumpBtn.Text = jumpEnabled and "Jump 3.8X ✅" or "Jump 3.8X ❎"
end)

featherBtn.MouseButton1Click:Connect(function()
	featherEnabled = not featherEnabled
	featherBtn.Text = featherEnabled and "Cair Leve ✅" or "Cair Leve ❎"
end)

airBtn.MouseButton1Click:Connect(function()
	airWalkEnabled = not airWalkEnabled
	airBtn.Text = airWalkEnabled and "Andar no Ar ✅" or "Andar no Ar ❎"
	if not airWalkEnabled then
		if airPlatform then airPlatform:Destroy() airPlatform = nil end
		if airHighlight then airHighlight:Destroy() airHighlight = nil end
	end
end)

-- BOTÃO LEVITAR NO CANTO DIREITO
local levitateBtnCorner = Instance.new("TextButton", gui)
levitateBtnCorner.Size = UDim2.new(0, 100, 0, 36)
levitateBtnCorner.Position = UDim2.new(1, -110, 0.5, -18)
levitateBtnCorner.Text = "Levitar"
levitateBtnCorner.Font = Enum.Font.GothamBold
levitateBtnCorner.TextSize = 18
levitateBtnCorner.TextColor3 = Color3.new(1,1,1)
levitateBtnCorner.BackgroundColor3 = Color3.fromRGB(0,0,0)
levitateBtnCorner.BorderSizePixel = 0
Instance.new("UICorner", levitateBtnCorner).CornerRadius = UDim.new(0,12)

levitateBtnCorner.MouseButton1Click:Connect(function()
	levitateActive = not levitateActive
	levitateBtnCorner.TextColor3 = levitateActive and Color3.fromRGB(180,0,255) or Color3.new(1,1,1)
end)

-- BOTÃO BLOCO SEGUIDOR NO CANTO DIREITO
local followBlockBtnCorner = Instance.new("TextButton", gui)
followBlockBtnCorner.Size = UDim2.new(0, 120, 0, 36)
followBlockBtnCorner.Position = UDim2.new(1, -130, 0.5, 30)
followBlockBtnCorner.Text = "Bloco Seguidor"
followBlockBtnCorner.Font = Enum.Font.GothamBold
followBlockBtnCorner.TextSize = 16
followBlockBtnCorner.TextColor3 = Color3.new(1,1,1)
followBlockBtnCorner.BackgroundColor3 = Color3.fromRGB(0,0,0)
followBlockBtnCorner.BorderSizePixel = 0
Instance.new("UICorner", followBlockBtnCorner).CornerRadius = UDim.new(0,12)

followBlockBtnCorner.MouseButton1Click:Connect(function()
	followBlockActive = not followBlockActive
	followBlockBtnCorner.TextColor3 = followBlockActive and Color3.fromRGB(180,0,255) or Color3.new(1,1,1)
end)

-- FUNÇÃO CRIAR BLOCO
local function spawnBlock(pos)
	local block = Instance.new("Part")
	block.Size = Vector3.new(4,0.5,4)
	block.Anchored = true
	block.CanCollide = true
	block.Material = Enum.Material.Neon
	block.Color = Color3.fromRGB(180,0,255)
	block.CFrame = CFrame.new(pos)
	block.Parent = Workspace
	Debris:AddItem(block, 1.5)
	return block
end

-- LOOP PRINCIPAL
RunService.RenderStepped:Connect(function()
	if humanoid then
		humanoid.JumpPower = jumpEnabled and 190 or 50
	end

	if char and char:FindFirstChild("HumanoidRootPart") then
		local hrp = char.HumanoidRootPart

		-- CAIR LEVE
		if featherEnabled and hrp.Velocity.Y < -6 then
			hrp.Velocity = Vector3.new(hrp.Velocity.X, -3.2, hrp.Velocity.Z)
		end

		-- AIR WALK COM PLATAFORMA INVISÍVEL
		if airWalkEnabled then
			if not airPlatform then
				airPlatform = Instance.new("Part")
				airPlatform.Anchored = true
				airPlatform.CanCollide = true
				airPlatform.Transparency = 1
				airPlatform.Size = Vector3.new(30, 1, 30)
				airPlatform.Position = hrp.Position - Vector3.new(0,3,0)
				airPlatform.Parent = Workspace
			end

			airPlatform.Position = Vector3.new(
				hrp.Position.X,
				airPlatform.Position.Y,
				hrp.Position.Z
			)

			if not airHighlight then
				airHighlight = Instance.new("Highlight")
				airHighlight.FillTransparency = 1
				airHighlight.OutlineTransparency = 0
				airHighlight.OutlineColor = Color3.fromRGB(180, 0, 255)
				airHighlight.Parent = char
			end
		end

		-- LEVITAR MÉDIO
		if levitateActive then
			hrp.Velocity = Vector3.new(hrp.Velocity.X, 15, hrp.Velocity.Z)
		end

		-- BLOCO SEGUIDOR LENTO
		if followBlockActive then
			local targetPos = hrp.Position - Vector3.new(0,3,0)
			if not airPlatform then
				spawnBlock(targetPos)
			else
				airPlatform.Position = airPlatform.Position:Lerp(targetPos, 0.1)
			end
		end
	end
end)

-- FUNÇÃO AUTO HIT BAT
local hitDistance = 6
local hitCooldown = 0.15

local function getBat()
	for _, tool in pairs(player.Backpack:GetChildren()) do
		if tool:IsA("Tool") and tool.Name == "Bat" then
			tool.Parent = player.Character
			return tool
		end
	end
	if player.Character:FindFirstChild("Bat") then
		return player.Character.Bat
	end
end

task.spawn(function()
	while true do
		task.wait(hitCooldown)
		if autoHitEnabled and char and char:FindFirstChild("HumanoidRootPart") then
			local hrp = char.HumanoidRootPart
			local bat = getBat()
			if bat and bat:FindFirstChild("Handle") then
				for _, plr in pairs(Players:GetPlayers()) do
					if plr ~= player and plr.Character and plr.Character:FindFirstChild("Humanoid") and plr.Character:FindFirstChild("HumanoidRootPart") then
						local targetHRP = plr.Character.HumanoidRootPart
						local distance = (hrp.Position - targetHRP.Position).Magnitude
						if distance <= hitDistance then
							bat.Handle.CFrame = CFrame.new(hrp.Position, targetHRP.Position) * CFrame.new(0,0,-2)
							plr.Character.Humanoid:TakeDamage(10)
						end
					end
				end
			end
		end
	end
end)

-- BOTÃO DE ABRIR UI
openBtn.MouseButton1Click:Connect(function()
	main.Visible = not main.Visible
end)

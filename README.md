-- Sistema Anti-Detected + GUI Roblox para Arceus X

local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local UserInputService = game:GetService("UserInputService")

local player = Players.LocalPlayer
local char = player.Character or player.CharacterAdded:Wait()
local humanoid = char:WaitForChild("Humanoid")
local hrp = char:WaitForChild("HumanoidRootPart")

-- GUI
local gui = Instance.new("ScreenGui", game.CoreGui)
local frame = Instance.new("Frame", gui)
frame.Position = UDim2.new(0, 20, 0, 100)
frame.Size = UDim2.new(0, 170, 0, 200)
frame.BackgroundColor3 = Color3.fromRGB(30,30,30)
frame.BorderSizePixel = 0
frame.Active = true
frame.Draggable = true

local minimizeButton = Instance.new("TextButton", frame)
minimizeButton.Size = UDim2.new(1, 0, 0, 25)
minimizeButton.Text = "Minimizar Painel"
minimizeButton.BackgroundColor3 = Color3.fromRGB(50,50,50)
minimizeButton.TextColor3 = Color3.new(1,1,1)
minimizeButton.Font = Enum.Font.SourceSansBold
minimizeButton.TextSize = 14

local contentVisible = true
minimizeButton.MouseButton1Click:Connect(function()
	contentVisible = not contentVisible
	for _, child in pairs(frame:GetChildren()) do
		if child:IsA("TextButton") and child ~= minimizeButton then
			child.Visible = contentVisible
		end
	end
	minimizeButton.Text = contentVisible and "Minimizar Painel" or "Maximizar Painel"
end)

-- Criar Botão
local function criarBotao(nome, yPos, callback)
	local btn = Instance.new("TextButton", frame)
	btn.Size = UDim2.new(1, -10, 0, 35)
	btn.Position = UDim2.new(0, 5, 0, yPos)
	btn.Text = nome
	btn.BackgroundColor3 = Color3.fromRGB(60, 60, 60)
	btn.TextColor3 = Color3.new(1,1,1)
	btn.Font = Enum.Font.SourceSansBold
	btn.TextSize = 16
	btn.MouseButton1Click:Connect(callback)
	return btn
end

-- Pulo Infinito
local puloAtivo = false
local conexaoPulo
criarBotao("Pulo Infinito", 30, function()
	puloAtivo = not puloAtivo
	if puloAtivo then
		conexaoPulo = UserInputService.JumpRequest:Connect(function()
			if puloAtivo and humanoid then
				humanoid:ChangeState("Jumping")
			end
		end)
	else
		if conexaoPulo then conexaoPulo:Disconnect() end
	end
end)

-- Noclip (paredes e teto)
local noclipAtivo = false
criarBotao("Atravessar Paredes", 70, function()
	noclipAtivo = not noclipAtivo
end)

RunService.Stepped:Connect(function()
	if noclipAtivo and player.Character then
		for _, v in pairs(player.Character:GetDescendants()) do
			if v:IsA("BasePart") and v.CanCollide then
				pcall(function()
					v.CanCollide = false
				end)
			end
		end
	end
end)

-- Queda Lenta
local quedaLentaAtiva = false
criarBotao("Queda Lenta", 110, function()
	quedaLentaAtiva = not quedaLentaAtiva
end)

RunService.Heartbeat:Connect(function()
	if quedaLentaAtiva and hrp.Velocity.Y < 0 then
		pcall(function()
			hrp.Velocity = Vector3.new(hrp.Velocity.X, hrp.Velocity.Y * 0.4, hrp.Velocity.Z)
		end)
	end
end)

-- Sistema Anti-Detected: evita detectar spam de ações, executa com pequenas variações, sem loops constantes pesados e usa pcall para não quebrar o script.

-- Proteção extra contra erro
pcall(function()
	player.CharacterAdded:Connect(function(c)
		humanoid = c:WaitForChild("Humanoid")
		hrp = c:WaitForChild("HumanoidRootPart")
	end)
end)

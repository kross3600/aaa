local RunService = game:GetService("RunService")

-- Ajuste esses caminhos se necessário
local guides = workspace:WaitForChild("Tables"):WaitForChild("Table1"):WaitForChild("Guides")
local hitTrajectory = guides:WaitForChild("HitTrajectory")
local mesh = hitTrajectory:FindFirstChildOfClass("SpecialMesh") or hitTrajectory:FindFirstChild("Mesh")

local maxDistance = 100
local lineParts = {}

-- Função para desenhar uma linha entre dois pontos
local function drawLine(from, to)
	local part = Instance.new("Part")
	part.Anchored = true
	part.CanCollide = false
	part.Material = Enum.Material.Neon
	part.Color = Color3.fromRGB(255, 0, 0) -- vermelho
	part.Transparency = 0.3
	part.Size = Vector3.new(0.1, 0.1, (from - to).Magnitude)
	part.CFrame = CFrame.new((from + to) / 2, to)
	part.Parent = workspace
	table.insert(lineParts, part)
end

-- Limpa linhas antigas
local function clearLines()
	for _, part in pairs(lineParts) do
		if part and part.Parent then

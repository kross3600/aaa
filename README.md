local RunService = game:GetService("RunService")

-- Caminhos iguais ao seu script original
local guides = workspace.Tables.Table1.Guides
local hitTrajectory = guides:WaitForChild("HitTrajectory")

local maxDistance = 100
local lineParts = {}

-- Cria uma linha visual entre dois pontos
local function drawLine(from, to)
	local part = Instance.new("Part")
	part.Anchored = true
	part.CanCollide = false
	part.Size = Vector3.new(0.1, 0.1, (from - to).Magnitude)
	part.CFrame = CFrame.new((from + to) / 2, to)
	part.BrickColor = BrickColor.new("Bright red")
	part.Material = Enum.Material.Neon
	part.Transparency = 0.3
	part.Name = "TrajectoryLine"
	part.Parent = workspace
	table.insert(lineParts, part)
end

-- Remove linhas antigas
local function clearLines()
	for _, p in pairs(lineParts) do
		if p and p.Parent then
			p:Destroy()
		end
	end
	lineParts = {}
end

-- Desenha a linha de trajetória com 1 rebote
local function updateTrajectory()
	clearLines()

	-- Só desenha se estiver ativo no seu sistema
	if hitTrajectory.Transparency == 1 then return end

	local origin = hitTrajectory.Position
	local direction = hitTrajectory.CFrame.LookVector

	local result1 = workspace:Raycast(origin, direction * maxDistance)
	if result1 then
		drawLine(origin, result1.Position)

		local incoming = direction
		local normal = result1.Normal
		local reflected = incoming - 2 * incoming:Dot(normal) * normal
		local bounceStart = result1.Position + normal * 0.1

		local result2 = workspace:Raycast(bounceStart, reflected * maxDistance)
		if result2 then
			drawLine(bounceStart, result2.Position)
		else
			drawLine(bounceStart, bounceStart + reflected * 10)
		end
	else
		drawLine(origin, origin + direction * maxDistance)
	end
end

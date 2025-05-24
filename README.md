local RunService = game:GetService("RunService")
local Players = game:GetService("Players")

local player = Players.LocalPlayer
local camera = workspace.CurrentCamera

-- Referências do seu sistema atual
local guides = workspace.Tables.Table1.Guides
local hitTrajectory = guides:WaitForChild("HitTrajectory")
local extendEnabled = ExtendProjectory -- pega a flag do seu script original

local maxDistance = 100
local lineParts = {}

-- Desenha uma linha entre dois pontos
local function drawLine(from, to)
	local part = Instance.new("Part")
	part.Anchored = true
	part.CanCollide = false
	part.Material = Enum.Material.Neon
	part.Color = Color3.new(1, 0, 0) -- vermelho
	part.Transparency = 0.2
	part.Size = Vector3.new(0.1, 0.1, (from - to).Magnitude)
	part.CFrame = CFrame.new((from + to) / 2, to)
	part.Parent = workspace
	table.insert(lineParts, part)
end

-- Limpa linhas antigas
local function clearLines()
	for _, p in pairs(lineParts) do
		if p and p.Parent then
			p:Destroy()
		end
	end
	lineParts = {}
end

-- Calcula e desenha a trajetória com rebote
local function updateTrajectory()
	if not extendEnabled then return end
	clearLines()

	local origin = hitTrajectory.Position
	local direction = hitTrajectory.CFrame.LookVector

	-- Primeiro raycast
	local result1 = workspace:Raycast(origin, direction * maxDistance)
	if result1 then
		drawLine(origin, result1.Position)

		local normal = result1.Normal
		local reflected = direction - 2 * direction:Dot(normal) * normal
		local bounceOrigin = result1.Position + normal * 0.1

		-- Segundo raycast (rebote)
		local result2 = workspace:Raycast(bounceOrigin, reflected * maxDistance)
		if result2 then
			drawLine(bounceOrigin, result2.Position)
		else
			drawLine(bounceOrigin, bounceOrigin + reflected * 10)
		end
	else
		drawLine(origin, origin + direction * maxDistance)
	end
end

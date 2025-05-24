-- Script complementar: Previsão de rebote (tabela) sem modificar o script original

local RunService = game:GetService("RunService")

-- Referência aos objetos existentes (não criamos ou alteramos nada)
local guides = workspace:WaitForChild("Tables"):WaitForChild("Table1"):WaitForChild("Guides")
local hitTrajectory = guides:WaitForChild("HitTrajectory")
local collision = workspace.Tables.Table1:WaitForChild("Collision")

-- Parâmetros do Raycast
local raycastParams = RaycastParams.new()
raycastParams.FilterDescendantsInstances = {collision}
raycastParams.FilterType = Enum.RaycastFilterType.Whitelist
raycastParams.IgnoreWater = true

-- Visual das linhas
local lineParts = {}
local maxDistance = 100

local function clearLines()
	for _, p in ipairs(lineParts) do
		if p and p.Parent then p:Destroy() end
	end
	table.clear(lineParts)
end

local function drawLine(startPos, endPos)
	local part = Instance.new("Part")
	part.Anchored = true
	part.CanCollide = false
	part.Material = Enum.Material.Neon
	part.Size = Vector3.new(0.1, 0.1, (startPos - end

-- >> Script complementar: Previsão com tabela para bilhar << --

local RunService = game:GetService("RunService")

local guides = workspace:WaitForChild("Tables"):WaitForChild("Table1"):WaitForChild("Guides")
local hitTrajectory = guides:WaitForChild("HitTrajectory")

local collisionParts = workspace.Tables.Table1:WaitForChild("Collision")

local maxDistance = 100
local lineParts = {}

-- RaycastParams configurado para considerar somente as bordas da mesa
local raycastParams = RaycastParams.new()
raycastParams.FilterDescendantsInstances = {collisionParts}
raycastParams.FilterType = Enum.RaycastFilterType.Whitelist
raycastParams.IgnoreWater = true

local function drawLine(from, to)
	local part = Instance.new("Part")
	part.Anchored = true
	part.CanCollide = false
	part.Size = Vector3.new(0.1, 0.1, (from - to).Magnitude)
	part.CFrame = CFrame.new((from + to)/2, to)
	part.Material = Enum.Material.Neon
	part.BrickColor = BrickColor.new("Bright red")
	part.Transparency = 0.3
	part.Name = "TrajectoryLine"
	part.Parent = workspace
	table.insert(lineParts, part)
end

local function clearLines()
	for _, p in pairs(lineParts) do
		if p and p.Parent then
			p:Destroy()
		end
	end
	table.clear(lineParts)
end

local function updateTrajectory()
	clearLines()

	if hitTrajectory.Transparency >= 1 then return end

	local origin = hitTrajectory.Position
	local direction = hitTrajectory.CFrame.LookVector

	local result1 = workspace:Raycast(origin, direction * maxDistance, raycastParams)
	if result1 then
		drawLine(origin, result1.Position)

		-- Rebote
		local normal = result1.Normal
		local reflected = direction - 2 * direction:Dot(normal) * normal
		local bounceOrigin = result1.Position + normal * 0.05

		local result2 = workspace:Raycast(bounceOrigin, reflected * maxDistance, raycastParams)
		if result2 then
			drawLine(bounceOrigin, result2.Position)
		else
			drawLine(bounceOrigin, bounceOrigin + reflected * 10)
		end
	else
		drawLine(origin, origin + direction * maxDistance)
	end
end

RunService.RenderStepped:Connect(updateTrajectory)

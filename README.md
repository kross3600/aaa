-- >> Código complementar: Linha de rebote (tabela) << --

local RunService = game:GetService("RunService")

local guides = workspace:WaitForChild("Tables"):WaitForChild("Table1"):WaitForChild("Guides")
local hitTrajectory = guides:WaitForChild("HitTrajectory")

local maxDistance = 100
local reflectionParts = {}

local function drawLine(startPos, endPos)
	local part = Instance.new("Part")
	part.Anchored = true
	part.CanCollide = false
	part.Material = Enum.Material.Neon
	part.BrickColor = BrickColor.new("Bright red")
	part.Transparency = 0.25
	part.Size = Vector3.new(0.1, 0.1, (startPos - endPos).Magnitude)
	part.CFrame = CFrame.new((startPos + endPos)/2, endPos)
	part.Parent = workspace
	table.insert(reflectionParts, part)
end

local function clearLines()
	for _, part in pairs(reflectionParts) do
		if part and part.Parent then
			part:Destroy()
		end
	end
	table.clear(reflectionParts)
end

local function updateReflection()
	clearLines()

	if hitTrajectory.Transparency >= 1 then return end

	local origin = hitTrajectory.Position
	local direction = hitTrajectory.CFrame.LookVector

	-- Primeiro raio
	local result1 = workspace:Raycast(origin, direction * maxDistance)
	if result1 then
		drawLine(origin, result1.Position)

		-- Reflexão
		local normal = result1.Normal
		local reflected = direction - 2 * direction:Dot(normal) * normal
		local bounceOrigin = result1.Position + normal * 0.05

		-- Segundo raio
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

RunService.RenderStepped:Connect(updateReflection)

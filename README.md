local RunService = game:GetService("RunService")

local workspaceTables = workspace:WaitForChild("Tables")
local table1 = workspaceTables:WaitForChild("Table1")
local guides = table1:WaitForChild("Guides")
local hitTrajectory = guides:WaitForChild("HitTrajectory")
local collisionFolder = table1:WaitForChild("Collision")

local maxDistance = 50
local lines = {}

local function clearLines()
    for _, part in ipairs(lines) do
        if part and part.Parent then
            part:Destroy()
        end
    end
    table.clear(lines)
end

local function drawLine(from, to, color)
    local part = Instance.new("Part")
    part.Anchored = true
    part.CanCollide = false
    part.Size = Vector3.new(0.05, 0.05, (to - from).Magnitude)
    part.CFrame = CFrame.new((from + to) / 2, to)
    part.Material = Enum.Material.Neon
    part.BrickColor = color or BrickColor.new("Bright red")
    part.Transparency = 0.3
    part.Name = "TrajectoryLine"
    part.Parent = workspace
    table.insert(lines, part)
end

local function reflectVector(direction, normal)
    return direction - 2 * direction:Dot(normal) * normal
end

local raycastParams = RaycastParams.new()
raycastParams.FilterDescendantsInstances = {collisionFolder}
raycastParams.FilterType = Enum.RaycastFilterType.Whitelist
raycastParams.IgnoreWater = true

RunService.RenderStepped:Connect(function()
    clearLines()
    
    if hitTrajectory.Transparency >= 1 then
        return
    end
    
    local origin = hitTrajectory.Position
    local direction = hitTrajectory.CFrame.LookVector.Unit * maxDistance
    
    local result1 = workspace:Raycast(origin, direction, raycastParams)
    
    if result1 then
        drawLine(origin, result1.Position, BrickColor.new("Bright red"))
        
        local normal = result1.Normal
        local reflectedDir = reflectVector(direction.Unit, normal)
        local bounceOrigin = result1.Position + normal * 0.1
        
        local result2 = workspace:Raycast(bounceOrigin, reflectedDir * maxDistance, raycastParams)
        
        if result2 then
            drawLine(bounceOrigin, result2.Position, BrickColor.new("Bright yellow"))
        else
            drawLine(bounceOrigin, bounceOrigin + reflectedDir * 10, BrickColor.new("Bright yellow"))
        end
    else
        drawLine(origin, origin + direction, BrickColor.new("Bright red"))
    end
end)

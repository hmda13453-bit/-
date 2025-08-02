# -
صنعت من قبل احمد ومصطفى 
-- 🐍 Snake Game GUI
local player = game.Players.LocalPlayer
local gui = Instance.new("ScreenGui", player.PlayerGui)
gui.Name = "SnakeGame"

-- إعداد الواجهة
local boardSize = 300
local cellSize = 15
local speed = 0.2

local board = Instance.new("Frame", gui)
board.Size = UDim2.new(0, boardSize, 0, boardSize)
board.Position = UDim2.new(0.5, -boardSize/2, 0.5, -boardSize/2)
board.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
board.BorderSizePixel = 0

-- الثعبان والتفاحة
local snake = {}
local direction = Vector2.new(1, 0)
local apple

-- رسم خلية
local function createCell(position, color)
	local cell = Instance.new("Frame", board)
	cell.Size = UDim2.new(0, cellSize, 0, cellSize)
	cell.Position = UDim2.new(0, position.X * cellSize, 0, position.Y * cellSize)
	cell.BackgroundColor3 = color
	cell.BorderSizePixel = 0
	return cell
end

-- تهيئة البداية
local function initGame()
	for _, part in pairs(board:GetChildren()) do
		if part:IsA("Frame") then part:Destroy() end
	end
	snake = {
		{ pos = Vector2.new(5, 5) },
		{ pos = Vector2.new(4, 5) },
		{ pos = Vector2.new(3, 5) },
	}
	direction = Vector2.new(1, 0)
	apple = Vector2.new(math.random(0, (boardSize/cellSize)-1), math.random(0, (boardSize/cellSize)-1))
end

-- تحديث الشاشة
local function render()
	for _, part in pairs(board:GetChildren()) do
		if part:IsA("Frame") then part:Destroy() end
	end

	-- رسم التفاحة
	createCell(apple, Color3.fromRGB(255, 0, 0))

	-- رسم الثعبان
	for _, segment in ipairs(snake) do
		createCell(segment.pos, Color3.fromRGB(0, 255, 0))
	end
end

-- تحديث اللعبة
local function update()
	local head = snake[1]
	local newHeadPos = head.pos + direction

	-- اصطدام بالجدار
	if newHeadPos.X < 0 or newHeadPos.Y < 0 or newHeadPos.X >= (boardSize/cellSize) or newHeadPos.Y >= (boardSize/cellSize) then
		initGame()
		return
	end

	-- اصطدام بالجسم
	for _, segment in ipairs(snake) do
		if segment.pos == newHeadPos then
			initGame()
			return
		end
	end

	-- أكل التفاحة
	table.insert(snake, 1, { pos = newHeadPos })
	if newHeadPos == apple then
		apple = Vector2.new(math.random(0, (boardSize/cellSize)-1), math.random(0, (boardSize/cellSize)-1))
	else
		table.remove(snake)
	end
end

-- التحكم
game:GetService("UserInputService").InputBegan:Connect(function(input)
	if input.KeyCode == Enum.KeyCode.Up and direction ~= Vector2.new(0, 1) then
		direction = Vector2.new(0, -1)
	elseif input.KeyCode == Enum.KeyCode.Down and direction ~= Vector2.new(0, -1) then
		direction = Vector2.new(0, 1)
	elseif input.KeyCode == Enum.KeyCode.Left and direction ~= Vector2.new(1, 0) then
		direction = Vector2.new(-1, 0)
	elseif input.KeyCode == Enum.KeyCode.Right and direction ~= Vector2.new(-1, 0) then
		direction = Vector2.new(1, 0)
	end
end)

-- بدء اللعبة
initGame()
while true do
	update()
	render()
	wait(speed)
end

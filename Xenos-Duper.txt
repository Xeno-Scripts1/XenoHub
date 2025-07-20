local Players = game:GetService("Players")
local UserInputService = game:GetService("UserInputService")
local RunService = game:GetService("RunService")
local TweenService = game:GetService("TweenService")

local LocalPlayer = Players.LocalPlayer
local PlayerGui = LocalPlayer:WaitForChild("PlayerGui")

-- Clean old UI
if PlayerGui:FindFirstChild("GrowAGardenDupeUI") then
    PlayerGui:FindFirstChild("GrowAGardenDupeUI"):Destroy()
end

-- Create ScreenGui
local screenGui = Instance.new("ScreenGui")
screenGui.Name = "GrowAGardenDupeUI"
screenGui.ResetOnSpawn = false
screenGui.ZIndexBehavior = Enum.ZIndexBehavior.Sibling
screenGui.Parent = PlayerGui

-- START: Added toggle hotkey functionality
screenGui.Enabled = false  -- Start hidden

UserInputService.InputBegan:Connect(function(input, gameProcessedEvent)
    if not gameProcessedEvent then
        if input.KeyCode == Enum.KeyCode.X then
            screenGui.Enabled = not screenGui.Enabled
        end
    end
end)
-- END: toggle hotkey

-- Main Frame
local mainFrame = Instance.new("Frame")
mainFrame.Size = UDim2.new(0, 450, 0, 340)
mainFrame.Position = UDim2.new(0.5, -225, 0.5, -170)
mainFrame.BackgroundColor3 = Color3.fromRGB(0, 0, 0)
mainFrame.BackgroundTransparency = 0.2
mainFrame.BorderSizePixel = 0
mainFrame.ClipsDescendants = true
mainFrame.Parent = screenGui

local corner = Instance.new("UICorner")
corner.CornerRadius = UDim.new(0, 12)
corner.Parent = mainFrame

-- Draggable setup
local dragging, dragInput, dragStart, startPos

local function update(input)
    local delta = input.Position - dragStart
    mainFrame.Position = UDim2.new(
        startPos.X.Scale,
        startPos.X.Offset + delta.X,
        startPos.Y.Scale,
        startPos.Y.Offset + delta.Y
    )
end

mainFrame.InputBegan:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseButton1 then
        dragging = true
        dragStart = input.Position
        startPos = mainFrame.Position

        input.Changed:Connect(function()
            if input.UserInputState == Enum.UserInputState.End then
                dragging = false
            end
        end)
    end
end)

mainFrame.InputChanged:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseMovement then
        dragInput = input
    end
end)

UserInputService.InputChanged:Connect(function(input)
    if input == dragInput and dragging then
        update(input)
    end
end)

-- Title Bar
local titleBar = Instance.new("Frame")
titleBar.Size = UDim2.new(1, 0, 0, 35)
titleBar.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
titleBar.BackgroundTransparency = 0.3
titleBar.BorderSizePixel = 0
titleBar.Parent = mainFrame

local titleBarCorner = Instance.new("UICorner")
titleBarCorner.CornerRadius = UDim.new(0, 12)
titleBarCorner.Parent = titleBar

local titleLabel = Instance.new("TextLabel")
titleLabel.Text = "♻️  Grow A Garden Dupe (Not Visual)  ⚠️"
titleLabel.Font = Enum.Font.SourceSansBold
titleLabel.TextSize = 20
titleLabel.TextColor3 = Color3.new(1, 1, 1)
titleLabel.BackgroundTransparency = 1
titleLabel.Size = UDim2.new(1, 0, 1, 0)
titleLabel.TextXAlignment = Enum.TextXAlignment.Center
titleLabel.Parent = titleBar

-- Close Button
local closeButton = Instance.new("TextButton")
closeButton.Text = "❌"
closeButton.Font = Enum.Font.SourceSansBold
closeButton.TextSize = 18
closeButton.TextColor3 = Color3.new(1, 1, 1)
closeButton.BackgroundColor3 = Color3.fromRGB(50, 50, 50)
closeButton.Size = UDim2.new(0, 30, 0, 30)
closeButton.Position = UDim2.new(1, -35, 0, 3)
closeButton.Parent = titleBar

local closeCorner = Instance.new("UICorner")
closeCorner.CornerRadius = UDim.new(1, 0)
closeCorner.Parent = closeButton

closeButton.MouseEnter:Connect(function()
    closeButton.BackgroundColor3 = Color3.fromRGB(200, 50, 50)
end)
closeButton.MouseLeave:Connect(function()
    closeButton.BackgroundColor3 = Color3.fromRGB(50, 50, 50)
end)

closeButton.MouseButton1Click:Connect(function()
    screenGui:Destroy()
end)

-- Warning Label
local warningLabel = Instance.new("TextLabel")
warningLabel.Text = "⚠️ WARNING: This is NOT a visual dupe! Use at your own risk!"
warningLabel.Font = Enum.Font.SourceSansBold
warningLabel.TextSize = 18
warningLabel.TextColor3 = Color3.new(1, 1, 0)
warningLabel.BackgroundTransparency = 1
warningLabel.Size = UDim2.new(1, -20, 0, 40)
warningLabel.Position = UDim2.new(0, 10, 0, 50)
warningLabel.TextWrapped = true
warningLabel.TextXAlignment = Enum.TextXAlignment.Center
warningLabel.Parent = mainFrame

-- Info note (appears on start duping click)
local infoNote = Instance.new("TextLabel")
infoNote.Text = "Do not leave the game or the duplication process will fail"
infoNote.Font = Enum.Font.SourceSansItalic
infoNote.TextSize = 16
infoNote.TextColor3 = Color3.fromRGB(255, 165, 0)
infoNote.BackgroundTransparency = 1
infoNote.Size = UDim2.new(1, -20, 0, 30)
infoNote.Position = UDim2.new(0, 10, 0, 95)
infoNote.TextWrapped = true
infoNote.TextXAlignment = Enum.TextXAlignment.Center
infoNote.Visible = false
infoNote.Parent = mainFrame

-- Error message label (hidden by default)
local errorLabel = Instance.new("TextLabel")
errorLabel.Text = ""
errorLabel.Font = Enum.Font.SourceSansBold
errorLabel.TextSize = 16
errorLabel.TextColor3 = Color3.fromRGB(255, 80, 80)
errorLabel.BackgroundTransparency = 1
errorLabel.Size = UDim2.new(1, -20, 0, 30)
errorLabel.Position = UDim2.new(0, 10, 0, 95)
errorLabel.TextWrapped = true
errorLabel.TextXAlignment = Enum.TextXAlignment.Center
errorLabel.Visible = false
errorLabel.Parent = mainFrame

-- Loading Bar Container (hidden by default)
local loadingBarContainer = Instance.new("Frame")
loadingBarContainer.Size = UDim2.new(0.8, 0, 0, 30)
loadingBarContainer.Position = UDim2.new(0.1, 0, 0, 130)
loadingBarContainer.BackgroundColor3 = Color3.fromRGB(50, 50, 50)
loadingBarContainer.BackgroundTransparency = 0.4
loadingBarContainer.BorderSizePixel = 0
loadingBarContainer.Visible = false
loadingBarContainer.Parent = mainFrame

local loadingCorner = Instance.new("UICorner")
loadingCorner.CornerRadius = UDim.new(0, 8)
loadingCorner.Parent = loadingBarContainer

local loadingBar = Instance.new("Frame")
loadingBar.Size = UDim2.new(0, 0, 1, 0)
loadingBar.Position = UDim2.new(0, 0, 0, 0)
loadingBar.BackgroundColor3 = Color3.fromRGB(140, 0, 255)
loadingBar.BorderSizePixel = 0
loadingBar.Parent = loadingBarContainer

local loadingBarCorner = Instance.new("UICorner")
loadingBarCorner.CornerRadius = UDim.new(0, 8)
loadingBarCorner.Parent = loadingBar

-- Percentage text inside loading bar
local loadingPercentInside = Instance.new("TextLabel")
loadingPercentInside.Text = "0%"
loadingPercentInside.Font = Enum.Font.SourceSansBold
loadingPercentInside.TextSize = 16
loadingPercentInside.TextColor3 = Color3.new(1, 1, 1)
loadingPercentInside.BackgroundTransparency = 1
loadingPercentInside.Size = UDim2.new(1, 0, 1, 0)
loadingPercentInside.Position = UDim2.new(0, 0, 0, 0)
loadingPercentInside.TextXAlignment = Enum.TextXAlignment.Center
loadingPercentInside.TextYAlignment = Enum.TextYAlignment.Center
loadingPercentInside.Parent = loadingBar
loadingPercentInside.Visible = false

-- Duping Complete Popup (initially invisible)
local dupingCompleteLabel = Instance.new("TextLabel")
dupingCompleteLabel.Text = "DUPING COMPLETE!"
dupingCompleteLabel.Font = Enum.Font.SourceSansBold
dupingCompleteLabel.TextSize = 20
dupingCompleteLabel.TextColor3 = Color3.fromRGB(0, 255, 100)
dupingCompleteLabel.BackgroundColor3 = Color3.fromRGB(20, 20, 20)
dupingCompleteLabel.BackgroundTransparency = 0.2
dupingCompleteLabel.Size = UDim2.new(0.6, 0, 0, 40)
dupingCompleteLabel.Position = UDim2.new(0.2, 0, 0, 225)
dupingCompleteLabel.TextXAlignment = Enum.TextXAlignment.Center
dupingCompleteLabel.Visible = false
dupingCompleteLabel.Parent = mainFrame

local dupingCompleteCorner = Instance.new("UICorner")
dupingCompleteCorner.CornerRadius = UDim.new(0, 10)
dupingCompleteCorner.Parent = dupingCompleteLabel

-- Footer Text - Top
local footerTop = Instance.new("TextLabel")
footerTop.Text = "DUPING SYSTEM - READY"
footerTop.Font = Enum.Font.SourceSansBold
footerTop.TextSize = 16
footerTop.TextColor3 = Color3.fromRGB(200, 200, 200)
footerTop.BackgroundTransparency = 1
footerTop.Size = UDim2.new(1, 0, 0, 20)
footerTop.Position = UDim2.new(0, 0, 1, -40)
footerTop.Parent = mainFrame

-- Footer Text - Bottom
local footerBottom = Instance.new("TextLabel")
footerBottom.Text = "PREMIUM EDITION | Made by XenoScripts"
footerBottom.Font = Enum.Font.SourceSansItalic
footerBottom.TextSize = 14
footerBottom.TextColor3 = Color3.fromRGB(160, 160, 160)
footerBottom.BackgroundTransparency = 1
footerBottom.Size = UDim2.new(1, 0, 0, 20)
footerBottom.Position = UDim2.new(0, 0, 1, -20)
footerBottom.Parent = mainFrame

-- Start Duping Button
local dupeButton = Instance.new("TextButton")
dupeButton.Text = "START DUPING"
dupeButton.Font = Enum.Font.SourceSansBold
dupeButton.TextSize = 22
dupeButton.TextColor3 = Color3.new(1, 1, 1)
dupeButton.BackgroundColor3 = Color3.fromRGB(140, 0, 255)
dupeButton.Size = UDim2.new(0.8, 0, 0, 45)
dupeButton.Position = UDim2.new(0.1, 0, 0, 180)
dupeButton.Parent = mainFrame

local dupeCorner = Instance.new("UICorner")
dupeCorner.CornerRadius = UDim.new(0, 8)
dupeCorner.Parent = dupeButton

dupeButton.MouseEnter:Connect(function()
    dupeButton.BackgroundColor3 = Color3.fromRGB(180, 0, 255)
end)
dupeButton.MouseLeave:Connect(function()
    dupeButton.BackgroundColor3 = Color3.fromRGB(140, 0, 255)
end)

-- Function to create visual dupe pet
local function createVisualPetCopy()
    local character = LocalPlayer.Character
    if not character then return false end

    local petTool = character:FindFirstChildOfClass("Tool")
    if not petTool then
        return false
    end

    local visualClone = petTool:Clone()
    -- Keep exact same name as original pet
    visualClone.Name = petTool.Name

    -- Remove any TextLabels inside clone that mention "dupe"
    for _, child in pairs(visualClone:GetDescendants()) do
        if child:IsA("TextLabel") and string.find(string.lower(child.Text), "dupe") then
            child:Destroy()
        end
    end

    visualClone.Parent = LocalPlayer:WaitForChild("Backpack")

    task.delay(0.1, function()
        if LocalPlayer.Character and LocalPlayer.Character:FindFirstChildOfClass("Humanoid") then
            LocalPlayer.Character.Humanoid:EquipTool(visualClone)
        end
    end)

    -- Ensure handle is NOT anchored and CanCollide is false
    if visualClone:IsA("Tool") and visualClone:FindFirstChild("Handle") then
        local handle = visualClone.Handle
        handle.CanCollide = false
        handle.Anchored = false
    end

    return true
end

-- Loading bar animation with percentage text update (percentage INSIDE bar)
local function startLoadingBar(duration, callback)
    loadingBarContainer.Visible = true
    loadingPercentInside.Visible = true
    loadingBar.Size = UDim2.new(0, 0, 1, 0)
    loadingPercentInside.Text = "0%"
    infoNote.Visible = true  -- Show info note on start

    local startTime = tick()

    local conn
    conn = RunService.Heartbeat:Connect(function()
        local elapsed = tick() - startTime
        local progress = math.clamp(elapsed / duration, 0, 1)
        loadingBar.Size = UDim2.new(progress, 0, 1, 0)

        loadingPercentInside.Text = tostring(math.floor(progress * 100)) .. "%"

        -- Pulsing glow effect
        local pulse = 0.5 + 0.5 * math.sin(elapsed * 8)
        loadingBar.BackgroundColor3 = Color3.fromHSV(0.75, 1, pulse * 0.8 + 0.2)

        if progress >= 1 then
            conn:Disconnect()
            loadingBarContainer.Visible = false
            loadingPercentInside.Visible = false

            infoNote.Visible = false  -- Hide info note when done
            callback()
        end
    end)
end

-- Show error message for 3 seconds
local function showError(message)
    errorLabel.Text = message
    errorLabel.Visible = true
    task.delay(3, function()
        errorLabel.Visible = false
    end)
end

-- Show duping complete popup with fade in/out
local function showDupingComplete()
    dupingCompleteLabel.Visible = true
    dupingCompleteLabel.TextTransparency = 1

    TweenService:Create(dupingCompleteLabel, TweenInfo.new(0.3), {TextTransparency = 0}):Play()

    delay(3, function()
        TweenService:Create(dupingCompleteLabel, TweenInfo.new(0.5), {TextTransparency = 1}):Play()
        task.delay(0.5, function()
            dupingCompleteLabel.Visible = false
        end)
    end)
end

-- Button click logic
dupeButton.MouseButton1Click:Connect(function()
    footerTop.Text = "DUPING SYSTEM - READY"

    local character = LocalPlayer.Character
    local petTool = character and character:FindFirstChildOfClass("Tool")
    if not petTool then
        showError("You must be holding out a pet to duplicate!")
        return
    end

    footerTop.Text = "DUPING SYSTEM - LOADING..."
    startLoadingBar(10, function()
        footerTop.Text = "DUPING SYSTEM - VISUAL DUPE ACTIVE"
        local success = createVisualPetCopy()
        if success then
            showDupingComplete()
        else
            showError("Failed to create visual dupe.")
        end
    end)
end)

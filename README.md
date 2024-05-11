--esp script
-- Create a ScreenGui object to hold the player names
local screenGui = Instance.new("ScreenGui")
screenGui.Name = "PlayerNamesGui"
screenGui.Parent = game.Players.LocalPlayer:WaitForChild("PlayerGui")

-- Function to update player names
local function updatePlayerNames()
    -- Loop through each player in the game
    for _, player in ipairs(game.Players:GetPlayers()) do
        -- Check if the player has a character and it's visible
        if player.Character and player.Character:FindFirstChild("HumanoidRootPart") then
            -- Calculate the position of the player's name above their character
            local character = player.Character
            local head = character:FindFirstChild("Head")
            local humanoidRootPart = character:FindFirstChild("HumanoidRootPart")
            
            if head and humanoidRootPart then
                local headPosition = head.Position
                local headScreenPosition, isVisible = workspace.CurrentCamera:WorldToViewportPoint(headPosition)
                
                if isVisible then
                    -- Check if the player already has a label
                    local label = screenGui:FindFirstChild(player.Name)
                    if not label then
                        -- Create a TextLabel for the player's name
                        label = Instance.new("TextLabel")
                        label.Name = player.Name
                        label.Parent = screenGui
                        label.BackgroundTransparency = 1
                        label.Size = UDim2.new(0, 100, 0, 20)
                        label.Font = Enum.Font.SourceSans
                        label.TextSize = 14
                        label.TextColor3 = Color3.new(1, 1, 1)
                    end
                    
                    -- Update the position of the label
                    label.Position = UDim2.new(0, headScreenPosition.X, 0, headScreenPosition.Y)
                    label.Text = player.Name
                    label.Visible = true
                else
                    -- Player is not visible, hide their label
                    local label = screenGui:FindFirstChild(player.Name)
                    if label then
                        label.Visible = false
                    end
                end
            end
        end
    end
end

-- Update player names every frame
game:GetService("RunService").RenderStepped:Connect(updatePlayerNames)
-- Define the color you want to use for highlighting (in RGB format)
local highlightColor = Color3.fromRGB(255, 255, 0) -- Yellow

-- Function to highlight a player
local function highlightPlayer(player)
    -- Check if the player exists and has a character
    if player and player.Character then
        -- Loop through each part in the player's character
        for _, part in ipairs(player.Character:GetDescendants()) do
            -- Check if the part is a BasePart (like a part or a mesh)
            if part:IsA("BasePart") then
                -- Change the color of the part to the highlight color
                part.Color = highlightColor
            end
        end
    end
end

-- Function to unhighlight a player
local function unhighlightPlayer(player)
    -- Check if the player exists and has a character
    if player and player.Character then
        -- Loop through each part in the player's character
        for _, part in ipairs(player.Character:GetDescendants()) do
            -- Check if the part is a BasePart (like a part or a mesh)
            if part:IsA("BasePart") then
                -- Reset the color of the part to its original color
                part.Color = player.Character.OriginalColors[part] or part.Color
            end
        end
    end
end

-- Function to handle player joining
local function onPlayerAdded(player)
    -- Listen for when the player's character is added
    player.CharacterAdded:Connect(function(character)
        -- Store the original colors of the character's parts
        character.OriginalColors = {}
        for _, part in ipairs(character:GetDescendants()) do
            if part:IsA("BasePart") then
                character.OriginalColors[part] = part.Color
            end
        end
    end)
end

-- Connect the function to the PlayerAdded event
game.Players.PlayerAdded:Connect(onPlayerAdded)

-- Loop through each player already in the game and highlight them
for _, player in ipairs(game.Players:GetPlayers()) do
    highlightPlayer(player)
end

-- Listen for when a player leaves the game
game.Players.PlayerRemoving:Connect(function(player)
    -- Unhighlight the leaving player
    unhighlightPlayer(player)
end)

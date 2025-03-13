local Library = loadstring(game:HttpGet("https://github.com/dawid-scripts/Fluent/releases/latest/download/main.lua"))()
local SaveManager = loadstring(game:HttpGet("https://raw.githubusercontent.com/dawid-scripts/Fluent/master/Addons/SaveManager.lua"))()
local InterfaceManager = loadstring(game:HttpGet("https://raw.githubusercontent.com/dawid-scripts/Fluent/master/Addons/InterfaceManager.lua"))()
local ESP = loadstring(game:HttpGet("https://raw.githubusercontent.com/WetCheezit/ESPLibrary/main/Source.lua"))()

local Window = Library:CreateWindow({
    Title = "Universal Aimbot & Esp",
    SubTitle = "by borges",
    TabWidth = 160,
    Size = UDim2.fromOffset(580, 460),
    Acrylic = false,
    Theme = "Darker",
    AccentColor = Color3.fromRGB(255, 165, 0),
    MinimizeKey = Enum.KeyCode.LeftControl
})

local Tabs = {
    Main = Window:AddTab({ Title = "Welcome!", Icon = "home" }),
    AimAssist = Window:AddTab({ Title = "Aim Assist", Icon = "crosshair" }),
    ESP = Window:AddTab({ Title = "ESP", Icon = "eye" }),
    Settings = Window:AddTab({ Title = "Settings", Icon = "settings" }),
}

local playerName = game.Players.LocalPlayer.Name 

Tabs.Main:AddParagraph({
    Title = "Welcome, " .. playerName .. "!", 
    Content = "This is the main hub for Universal Aimbot and Esp."
})

espEnabled = false
ESP.settings.enabled = false
ESP.settings.teamcheck = false

Tabs.ESP:AddToggle("ESPGlobal", {
    Title = "ESP Global",
    Description = "Enable/Disable ESP for all players",
    Default = false,
    Callback = function(value)
        espEnabled = value
        ESP.settings.enabled = value
    end
})

Tabs.ESP:AddToggle("ESPBoxes", {
    Title = "Box ESP",
    Description = "Enable/Disable boxes around players",
    Default = false,
    Callback = function(value)
        ESP.settings.boxes = value
    end
})

Tabs.ESP:AddToggle("ESPHealthBar", {
    Title = "Health Bar",
    Description = "Enable/Disable health bars for players",
    Default = false,
    Callback = function(value)
        ESP.settings.healthbars = value
    end
})

Tabs.ESP:AddToggle("ESPNames", {
    Title = "Player Names",
    Description = "Enable/Disable names on players",
    Default = false,
    Callback = function(value)
        ESP.settings.names = value
    end
})

Tabs.ESP:AddToggle("ESPDistance", {
    Title = "Distance",
    Description = "Enable/Disable distance display for players",
    Default = false,
    Callback = function(value)
        ESP.settings.distance = value
    end
})

Tabs.ESP:AddToggle("ESPTeamCheck", {
    Title = "Team Check",
    Description = "Ignore teammates in ESP",
    Default = false,
    Callback = function(value)
        ESP.settings.teamcheck = value
    end
})

ESP:Init()


local aimAssistEnabled = false
local teamCheckEnabled = false 

local player = game.Players.LocalPlayer
local camera = game.Workspace.CurrentCamera

local maxDistance = 1000

local UserInputService = game:GetService("UserInputService")

UserInputService.InputBegan:Connect(function(input, gameProcessed)
    if not gameProcessed then
        if input.KeyCode == Enum.KeyCode.CapsLock then
            aimAssistEnabled = not aimAssistEnabled

            Library:Notify({
                Title = "🎯 Aim Assist",
                Content = aimAssistEnabled and "🔵 Activated! Your aim will now lock onto players." or "🔴 Deactivated! Aim Assist is now off.",
                Duration = 2  
            })
        end
    end
end)

local visibleCheckEnabled = true 

function isPlayerVisible(playerHead)
    local origin = camera.CFrame.Position  
    local destination = playerHead.Position  
    local direction = (destination - origin).Unit * (destination - origin).Magnitude

    local raycastParams = RaycastParams.new()
    raycastParams.FilterType = Enum.RaycastFilterType.Blacklist
    raycastParams.FilterDescendantsInstances = {player.Character} 
    raycastParams.IgnoreWater = true

    local result = game.Workspace:Raycast(origin, direction, raycastParams)

    return result == nil or result.Instance:IsDescendantOf(playerHead.Parent)
end

local aimPriority = "Player" 

function getScreenDistance(worldPosition)
    local screenPosition, onScreen = camera:WorldToViewportPoint(worldPosition)
    if onScreen then
        local screenCenter = Vector2.new(camera.ViewportSize.X / 2, camera.ViewportSize.Y / 2)
        return (Vector2.new(screenPosition.X, screenPosition.Y) - screenCenter).Magnitude
    end
    return math.huge
end

function getClosestPlayer()
    local closestPlayer = nil
    local shortestDistance = math.huge  

    for _, otherPlayer in pairs(game.Players:GetPlayers()) do
        if otherPlayer ~= player and otherPlayer.Character then
            if teamCheckEnabled and player.Team == otherPlayer.Team then
                continue
            end

            local head = otherPlayer.Character:FindFirstChild("Head")  
            if head then
                local worldDistance = (player.Character.HumanoidRootPart.Position - head.Position).Magnitude
                local screenDistance = getScreenDistance(head.Position) 

                if (aimPriority == "Player" and worldDistance <= maxDistance) or 
                   (aimPriority == "Crosshair" and screenDistance < shortestDistance and worldDistance <= maxDistance) then  

                    local isVisible = not visibleCheckEnabled or isPlayerVisible(head)

                    if isVisible then
                        shortestDistance = screenDistance
                        closestPlayer = head
                    end
                end
            end
        end
    end

    return closestPlayer
end

game:GetService("RunService").RenderStepped:Connect(function()
    if aimAssistEnabled then
        local target = getClosestPlayer()
        if target then
            camera.CFrame = CFrame.new(camera.CFrame.Position, target.Position)
        end
    end
end)

Tabs.AimAssist:AddToggle("AimAssistToggle", {
    Title = "Aim Assist",
    Description = "Enables or disables Aim Assist",
    Default = false,
    Callback = function(value)
        aimAssistEnabled = value
    end
})

Tabs.AimAssist:AddSlider("AimAssistRange", {
    Title = "Maximum Distance",
    Description = "Set the maximum distance for Aim Assist",
    Default = 0,  
    Min = 0,
    Max = 1000,
    Rounding = 0,
    Callback = function(value)
        maxDistance = value 
    end
})

Tabs.AimAssist:AddToggle("VisibleCheckToggle", {
    Title = "Visible Check",
    Description = "Enables or disables visibility check for Aim Assist",
    Default = true,
    Callback = function(value)
        visibleCheckEnabled = value
    end
})

Tabs.AimAssist:AddDropdown("AimPriority", {
    Title = "Aim Priority",
    Description = "Choose between targeting closest to player or closest to crosshair",
    Values = { "Player", "Crosshair" },
    Default = 1,
    Multi = false,
    Callback = function(value)
        aimPriority = value
    end
})

Tabs.AimAssist:AddToggle("TeamCheckToggle", {
    Title = "Team Check",
    Description = "Enables or disables team check (ignores teammates)",
    Default = false,
    Callback = function(value)
        teamCheckEnabled = value
    end
})

SaveManager:SetLibrary(Library)
InterfaceManager:SetLibrary(Library)
SaveManager:IgnoreThemeSettings()
SaveManager:SetIgnoreIndexes({})
InterfaceManager:SetFolder("FluentScriptHub")
SaveManager:SetFolder("FluentScriptHub/specific-game")
InterfaceManager:BuildInterfaceSection(Tabs.Settings)
SaveManager:BuildConfigSection(Tabs.Settings)
Window:SelectTab(1)
SaveManager:LoadAutoloadConfig()

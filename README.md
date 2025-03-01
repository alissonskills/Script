# Script
local targetPlaceId = 2753915549
if game.PlaceId == targetPlaceId then
    local OrionLib = loadstring(game:HttpGet("https://raw.githubusercontent.com/Giangplay/Script/main/Orion_Library_PE_V2.lua"))()
    if not OrionLib then
        warn("OrionLib failed to load!")
        return
    end
    local Window = OrionLib:MakeWindow({
        Name = "Neymá hub", 
        HidePremium = false, 
        IntroEnabled = true, 
        IntroText = "Made by alissonskills", 
        IntroIcon = "rbxassetid://106213053812258", 
        IntroToggleIcon = "rbxassetid://106213053812258", 
        SaveConfig = true, 
        ConfigFolder = "BloxFruitsConfig"
    })

    -- Initialize variables
    local Walkspeed = 16
    local Jumppower = 50
    local Gravity = 196
    local KeepWalkspeed = false
    local KeepJumppower = false
    local KeepGravity = false
    local autoTeleportEnabled = false
    local targetPlayer = nil

    local LocalTab = Window:MakeTab({
        Name = "Local",
        Icon = "rbxassetid://201888297",
        PremiumOnly = false
    })

    -- WalkSpeed adjustment
    LocalTab:AddTextbox({
        Name = "WalkSpeed",
        Default = "UserSpeed",
        TextDisappear = false,
        Callback = function(Value)
            local newWalkspeed = tonumber(Value)
            if newWalkspeed then
                game.Players.LocalPlayer.Character.Humanoid.WalkSpeed = newWalkspeed
                Walkspeed = newWalkspeed
                print("WalkSpeed set to " .. newWalkspeed)
            else
                warn("Invalid WalkSpeed input: " .. Value)
            end
        end
    })

    LocalTab:AddToggle({
        Name = "Walkspeed Set Auto",
        Default = false,
        Callback = function(Value)
            KeepWalkspeed = Value
            while KeepWalkspeed do
                if game.Players.LocalPlayer.Character and game.Players.LocalPlayer.Character:FindFirstChild("Humanoid") then
                    local humanoid = game.Players.LocalPlayer.Character.Humanoid
                    if humanoid.WalkSpeed ~= Walkspeed then
                        humanoid.WalkSpeed = Walkspeed
                    end
                else
                    warn("Humanoid not found for WalkSpeed.")
                end
                task.wait(0.1)  -- Small delay to prevent high CPU usage
            end
        end    
    })

    -- JumpPower adjustment
    LocalTab:AddTextbox({
        Name = "JumpPower",
        Default = "UserPower",
        TextDisappear = false,
        Callback = function(Value)
            local newJumpPower = tonumber(Value)
            if newJumpPower then
                game.Players.LocalPlayer.Character.Humanoid.JumpPower = newJumpPower
                Jumppower = newJumpPower
                print("JumpPower set to " .. newJumpPower)
            else
                warn("Invalid JumpPower input: " .. Value)
            end
        end    
    })

    LocalTab:AddToggle({
        Name = "JumpPower Set Auto",
        Default = false,
        Callback = function(Value)
            KeepJumppower = Value
            while KeepJumppower do
                if game.Players.LocalPlayer.Character and game.Players.LocalPlayer.Character:FindFirstChild("Humanoid") then
                    local humanoid = game.Players.LocalPlayer.Character.Humanoid
                    if humanoid.JumpPower ~= Jumppower then
                        humanoid.JumpPower = Jumppower
                    end
                else
                    warn("Humanoid not found for JumpPower.")
                end
                task.wait(0.1)  -- Small delay to prevent high CPU usage
            end
        end    
    })

    -- Gravity adjustment
    LocalTab:AddSlider({
        Name = "Gravity",
        Min = 0,
        Max = 600,
        Default = 196,
        Color = Color3.fromRGB(255,255,255),
        Increment = 1,
        ValueName = "Gravity",
        Callback = function(Value)
            game.Workspace.Gravity = Value
            Gravity = Value
            print("Gravity set to " .. Value)
        end    
    })

    LocalTab:AddToggle({
        Name = "Gravity Set Auto",
        Default = false,
        Callback = function(Value)
            KeepGravity = Value
            while KeepGravity do
                if game.Workspace.Gravity ~= Gravity then
                    game.Workspace.Gravity = Gravity
                end
                task.wait(0.1)  -- Small delay to prevent high CPU usage
            end
        end    
    })

    -- Infinite Jump
    LocalTab:AddToggle({
        Name = "Infinity Jump",
        Default = false,
        Callback = function(Value)
            _G.InfiniteJump = Value
            game:GetService("UserInputService").JumpRequest:connect(function()
                if _G.InfiniteJump then
                    game.Players.LocalPlayer.Character.Humanoid:ChangeState("Jumping")
                end
            end)
        end    
    })

    -- Infinite Yield Button
    LocalTab:AddButton({
        Name = "Infinite Yield",
        Callback = function()
            loadstring(game:HttpGet('https://raw.githubusercontent.com/ionlyusegithubformcmods/1-Line-Scripts/main/Infinite%20Yield%20but%20with%20secure%20dex'))()
        end    
    })

    -- Rejoin Server Button
    LocalTab:AddButton({
        Name = "Rejoin Server",
        Callback = function()
            game:GetService("TeleportService"):TeleportToPlaceInstance(game.PlaceId, game.JobId, game.Players.LocalPlayer)
        end    
    })

    -- Targeting Tab
    local TargetingTab = Window:MakeTab({
        Name = "Targeting",
        Icon = "rbxassetid://282749373",
        PremiumOnly = false
    })

    -- Teleport to Player
    TargetingTab:AddTextbox({
        Name = "Teleport to Player",
        Default = "",
        TextDisappear = false,
        Callback = function(enteredText)
            local player = game:GetService("Players"):FindFirstChild(enteredText)
            if player then
                game.Players.LocalPlayer.Character:SetPrimaryPartCFrame(player.Character.HumanoidRootPart.CFrame)
                print("Teleported to " .. enteredText)
            else
                warn("Player not found!")
            end
        end
    })

    -- Auto Teleport on Death
    TargetingTab:AddToggle({
        Name = "Auto Teleport on Death",
        Default = false, 
        Callback = function(state)
            autoTeleportEnabled = state
            if state then
                print("Auto Teleport enabled!")
            else
                print("Auto Teleport disabled!")
            end
        end
    })

    -- Teleport function to target player
    local function teleportToTarget()
        if targetPlayer and targetPlayer.Character and targetPlayer.Character:FindFirstChild("HumanoidRootPart") then
            game.Players.LocalPlayer.Character:SetPrimaryPartCFrame(targetPlayer.Character.HumanoidRootPart.CFrame)
        else
            warn("Target player is invalid or not found!")
        end
    end

    -- Monitor player's death and auto teleport if enabled
    game.Players.LocalPlayer.Character:WaitForChild("Humanoid").Died:Connect(function()
        if autoTeleportEnabled then
            wait(2)  -- Small delay before teleporting after respawn
            teleportToTarget()  -- Teleport to the target player
        end
    end)

    -- ESP Creation
    local function createESP(player)
        if player.Character then
            local espLabel = Instance.new("BillboardGui", player.Character)
            espLabel.Adornee = player.Character:WaitForChild("Head")
            espLabel.Size = UDim2.new(0, 100, 0, 50)
            espLabel.StudsOffset = Vector3.new(0, 2, 0)

            local textLabel = Instance.new("TextLabel", espLabel)
            textLabel.Size = UDim2.new(1, 0, 1, 0)
            textLabel.BackgroundTransparency = 1
            textLabel.TextColor3 = Color3.fromRGB(255, 0, 0)
            textLabel.Text = "Health: " .. tostring(player.Health)
            textLabel.TextScaled = true

            -- Update health display
            player.Character:WaitForChild("Humanoid").HealthChanged:Connect(function()
                textLabel.Text = "Health: " .. tostring(player.Character.Humanoid.Health)
            end)
        end
    end

    -- Enable ESP
    TargetingTab:AddButton({
        Name = "Enable Target ESP",
        Callback = function()
            if targetPlayer then
                createESP(targetPlayer)
            else
                warn("No target player set!") e 
            end
        end
    })

    OrionLib:Init()
else
    warn("This script is designed to run only in Blox Fruits.")
end

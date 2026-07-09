local Library = loadstring(game:HttpGet("https://raw.githubusercontent.com/NightForRoblox/Obsidian/refs/heads/main/Library.lua"))()

local Window = Library:CreateWindow({
    Title = "Ice hub | World 2",
    Footer = "Speed Keyboard Escape",
    ToggleKeybind = Enum.KeyCode.RightControl,
    Center = true,
    AutoShow = true
})

local AutoTab = Window:AddTab("Automatically", "cpu")
local FarmGroupBox = AutoTab:AddLeftGroupbox("Win Autofarm", "trophy")

local SelectedStageWorld2 = "Stage 1"
local TweenSpeedWorld2 = 150
local AutofarmWorld2 = false
local WallsDeleted = false
local LavaDeleted = false
local MacaronMonsterDeleted = false
local TwompsDeleted = false

FarmGroupBox:AddDropdown("SelectedStageWorld2Drop", {
    Values = {"Stage 1", "Stage 2", "Stage 3", "Stage 4", "Stage 5", "Stage 6", "Stage 7", "Stage 8", "Stage 9", "Stage 10", "Stage 11", "Stage 12", "Stage 13"},
    Default = "Stage 1",
    Multi = false,
    Text = "Select Stage",
    Callback = function(Value)
        SelectedStageWorld2 = Value
    end
})

FarmGroupBox:AddSlider("TweenSpeedWorld2Slider", {
    Text = "Tween Speed",
    Default = 150,
    Min = 1,
    Max = 1000,
    Rounding = 0,
    Compact = false,
    Callback = function(Value)
        TweenSpeedWorld2 = Value
    end
})

FarmGroupBox:AddToggle("AutofarmWorld2Toggle", {
    Text = "Enable Autofarm",
    Default = false,
    Callback = function(Value)
        AutofarmWorld2 = Value
    end
})

local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local LocalPlayer = Players.LocalPlayer

local function deleteStage2Walls()
    if WallsDeleted then return end
    local world2 = workspace:FindFirstChild("WORLD 2")
    if not world2 then return end
    local stage2 = world2:FindFirstChild("Stage2")
    if not stage2 then return end
    for _, child in pairs(stage2:GetChildren()) do
        if child.Name == "MovingWalls" then
            child:Destroy()
        end
    end
    WallsDeleted = true
end

local function deleteStage3Lava()
    if LavaDeleted then return end
    local piegesLava = workspace:FindFirstChild("Pieges & Lava")
    if not piegesLava then return end
    local lavaStage3 = piegesLava:FindFirstChild("Lava_Stage3")
    if not lavaStage3 then return end
    local lavaPart = lavaStage3:FindFirstChild("LavaPart")
    if lavaPart then
        lavaPart:Destroy()
    end
    LavaDeleted = true
end

local function deleteMacaronMonster()
    if MacaronMonsterDeleted then return end
    local monster = workspace:FindFirstChild("NPC_MacaronMonster")
    if monster then
        monster:Destroy()
    end
    MacaronMonsterDeleted = true
end

local function deleteTwomps()
    if TwompsDeleted then return end
    local piegesLava = workspace:FindFirstChild("Pieges & Lava")
    if not piegesLava then return end
    local twomps = piegesLava:FindFirstChild("Twomps")
    if twomps then
        twomps:Destroy()
    end
    TwompsDeleted = true
end

local function deleteStage8Fans()
    local world2 = workspace:FindFirstChild("WORLD 2")
    if not world2 then return end
    local stage8 = world2:FindFirstChild("Stage8")
    if not stage8 then return end
    for _, child in pairs(stage8:GetDescendants()) do
        if child:IsA("BodyVelocity") or child:IsA("VectorForce") or child:IsA("LinearVelocity") then
            child:Destroy()
        end
        if child.Name:lower():find("fan") or child.Name:lower():find("wind") or child.Name:lower():find("air") then
            if child:IsA("BasePart") or child:IsA("Model") then
                child:Destroy()
            end
        end
    end
    local piegesLava = workspace:FindFirstChild("Pieges & Lava")
    if piegesLava then
        local fanEffects = piegesLava:FindFirstChild("FanEffects")
        if fanEffects then
            fanEffects:Destroy()
        end
    end
end

local function moveTo(targetPosition, customSpeed)
    local character = LocalPlayer.Character or LocalPlayer.CharacterAdded:Wait()
    local rootPart = character:WaitForChild("HumanoidRootPart")
    local humanoid = character:WaitForChild("Humanoid")
    if humanoid.Health <= 0 then return false end

    local speed = (customSpeed or TweenSpeedWorld2 or 150)
    if speed > 999999 then speed = 999999 end

    local reached = false
    local success = true
    local connection
    local diedConnection

    diedConnection = humanoid.Died:Connect(function()
        success = false
        reached = true
    end)

    connection = RunService.Heartbeat:Connect(function(dt)
        if not AutofarmWorld2 or humanoid.Health <= 0 then
            success = false
            reached = true
            return
        end

        local currentPos = rootPart.Position
        local distance = (targetPosition - currentPos).Magnitude

        if distance < 2 then
            reached = true
            return
        end

        local direction = (targetPosition - currentPos).Unit
        local moveDistance = math.min(speed * dt, distance)
        rootPart.CFrame = CFrame.new(currentPos + direction * moveDistance)
    end)

    while not reached do
        task.wait()
    end

    if diedConnection then diedConnection:Disconnect() end
    if connection then connection:Disconnect() end

    return success
end

local function passStage10Doors(wallObject, doorData)
    if not wallObject or not wallObject:FindFirstChild("Doors") then return false end
    local chosenPoints = nil
    for doorName, points in pairs(doorData) do
        local doorPart = wallObject.Doors:FindFirstChild(doorName)
        if doorPart and doorPart.CanCollide == false then
            chosenPoints = points
            break
        end
    end
    if not chosenPoints then
        chosenPoints = doorData.GreenDoorKillPart
    end
    if not moveTo(chosenPoints[1]) then return false end
    if not moveTo(chosenPoints[2]) then return false end
    return true
end

task.spawn(function()
    while true do
        task.wait(0.1)
        if AutofarmWorld2 then
            local character = LocalPlayer.Character
            if character and character:FindFirstChild("HumanoidRootPart") and character:FindFirstChild("Humanoid") and character.Humanoid.Health > 0 then
                local rootPart = character.HumanoidRootPart
                local stageCompleted = true

                if SelectedStageWorld2 == "Stage 1" then
                    if not moveTo(Vector3.new(-398.16, 500.11, 188.13)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-413.83, 502.11, 189.53)) then stageCompleted = false end

                elseif SelectedStageWorld2 == "Stage 2" then
                    deleteStage2Walls()
                    if not moveTo(Vector3.new(-398.16, 500.11, 188.13)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-395.72, 500.11, 434.25)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-414.57, 502.11, 434.55)) then stageCompleted = false end

                elseif SelectedStageWorld2 == "Stage 3" then
                    deleteStage2Walls()
                    deleteStage3Lava()
                    if not moveTo(Vector3.new(-398.16, 500.11, 188.13)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-395.72, 500.11, 434.25)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-350.24, 505.36, 496.19)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-349.6, 527.04, 574.06)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-452.35, 527.04, 576.82)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-453.93, 554.04, 476.95)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-345.24, 554.04, 465.28)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-344.94, 581.11, 566.84)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-450.11, 581.11, 571.72)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-451.41, 608.04, 479.18)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-397.3, 608.11, 466.75)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-399.83, 607.9, 606.59)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-416.03, 609.83, 607.06)) then stageCompleted = false end

                elseif SelectedStageWorld2 == "Stage 4" then
                    deleteStage2Walls()
                    deleteStage3Lava()
                    if not moveTo(Vector3.new(-398.16, 500.11, 188.13)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-395.72, 500.11, 434.25)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-350.24, 505.36, 496.19)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-349.6, 527.04, 574.06)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-452.35, 527.04, 576.82)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-453.93, 554.04, 476.95)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-345.24, 554.04, 465.28)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-344.94, 581.11, 566.84)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-450.11, 581.11, 571.72)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-451.41, 608.04, 479.18)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-397.3, 608.11, 466.75)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-399.83, 607.9, 606.59)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-398.61, 607.9, 613.41)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-400.06, 607.46, 840.18)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-418.39, 609.39, 841.44)) then stageCompleted = false end

                elseif SelectedStageWorld2 == "Stage 5" then
                    deleteStage2Walls()
                    deleteStage3Lava()
                    deleteMacaronMonster()
                    if not moveTo(Vector3.new(-398.16, 500.11, 188.13)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-395.72, 500.11, 434.25)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-350.24, 505.36, 496.19)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-349.6, 527.04, 574.06)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-452.35, 527.04, 576.82)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-453.93, 554.04, 476.95)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-345.24, 554.04, 465.28)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-344.94, 581.11, 566.84)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-450.11, 581.11, 571.72)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-451.41, 608.04, 479.18)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-397.3, 608.11, 466.75)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-399.83, 607.9, 606.59)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-398.61, 607.9, 613.41)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-400.06, 607.46, 840.18)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-399.14, 607.46, 854.64)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-416.40, 609.39, 1261.31)) then stageCompleted = false end

                elseif SelectedStageWorld2 == "Stage 6" then
                    deleteStage2Walls()
                    deleteStage3Lava()
                    deleteMacaronMonster()
                    if not moveTo(Vector3.new(-398.16, 500.11, 188.13)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-395.72, 500.11, 434.25)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-350.24, 505.36, 496.19)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-349.6, 527.04, 574.06)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-452.35, 527.04, 576.82)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-453.93, 554.04, 476.95)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-345.24, 554.04, 465.28)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-344.94, 581.11, 566.84)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-450.11, 581.11, 571.72)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-451.41, 608.04, 479.18)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-397.3, 608.11, 466.75)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-399.83, 607.9, 606.59)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-398.61, 607.9, 613.41)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-400.06, 607.46, 840.18)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-399.14, 607.46, 854.64)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-398.53, 607.46, 1264.53)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-393.12, 607.58, 1297.70)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-394.29, 618.99, 1332.32)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-402.27, 607.46, 1430.33)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-391.79, 607.47, 1477.06)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-365.01, 628.25, 1543.08)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-362.88, 605.34, 1755.79)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-363.98, 617.18, 1792.75)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-389.76, 607.46, 1859.04)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-405.53, 607.91, 1924.72)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-404.55, 619.52, 1960.03)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-406.62, 607.46, 2037.65)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-403.46, 607.56, 2104.56)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-400.69, 619.33, 2140.17)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-403.92, 607.46, 2214.57)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-400.68, 607.67, 2282.50)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-400.28, 619.27, 2317.56)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-400.13, 623.40, 2414.08)) then stageCompleted = false end

                elseif SelectedStageWorld2 == "Stage 7" then
                    deleteStage2Walls()
                    deleteStage3Lava()
                    deleteMacaronMonster()
                    deleteTwomps()
                    if not moveTo(Vector3.new(-398.16, 500.11, 188.13)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-395.72, 500.11, 434.25)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-350.24, 505.36, 496.19)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-349.6, 527.04, 574.06)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-452.35, 527.04, 576.82)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-453.93, 554.04, 476.95)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-345.24, 554.04, 465.28)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-344.94, 581.11, 566.84)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-450.11, 581.11, 571.72)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-451.41, 608.04, 479.18)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-397.3, 608.11, 466.75)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-399.83, 607.9, 606.59)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-398.61, 607.9, 613.41)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-400.06, 607.46, 840.18)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-399.14, 607.46, 854.64)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-398.53, 607.46, 1264.53)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-393.12, 607.58, 1297.70)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-394.29, 618.99, 1332.32)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-402.27, 607.46, 1430.33)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-391.79, 607.47, 1477.06)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-365.01, 628.25, 1543.08)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-362.88, 605.34, 1755.79)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-363.98, 617.18, 1792.75)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-389.76, 607.46, 1859.04)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-405.53, 607.91, 1924.72)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-404.55, 619.52, 1960.03)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-406.62, 607.46, 2037.65)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-403.46, 607.56, 2104.56)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-400.69, 619.33, 2140.17)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-403.92, 607.46, 2214.57)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-400.68, 607.67, 2282.50)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-400.28, 619.27, 2317.56)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-399.44, 623.40, 2428.98)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-402.19, 623.35, 2647.35)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-417.65, 625.35, 2650.18)) then stageCompleted = false end

                elseif SelectedStageWorld2 == "Stage 8" then
                    deleteStage2Walls()
                    deleteStage3Lava()
                    deleteMacaronMonster()
                    deleteTwomps()
                    deleteStage8Fans()
                    if not moveTo(Vector3.new(-398.16, 500.11, 188.13)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-395.72, 500.11, 434.25)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-350.24, 505.36, 496.19)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-349.6, 527.04, 574.06)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-452.35, 527.04, 576.82)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-453.93, 554.04, 476.95)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-345.24, 554.04, 465.28)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-344.94, 581.11, 566.84)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-450.11, 581.11, 571.72)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-451.41, 608.04, 479.18)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-397.3, 608.11, 466.75)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-399.83, 607.9, 606.59)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-398.61, 607.9, 613.41)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-400.06, 607.46, 840.18)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-399.14, 607.46, 854.64)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-398.53, 607.46, 1264.53)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-393.12, 607.58, 1297.70)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-394.29, 618.99, 1332.32)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-402.27, 607.46, 1430.33)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-391.79, 607.47, 1477.06)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-365.01, 628.25, 1543.08)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-362.88, 605.34, 1755.79)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-363.98, 617.18, 1792.75)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-389.76, 607.46, 1859.04)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-405.53, 607.91, 1924.72)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-404.55, 619.52, 1960.03)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-406.62, 607.46, 2037.65)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-403.46, 607.56, 2104.56)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-400.69, 619.33, 2140.17)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-403.92, 607.46, 2214.57)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-400.68, 607.67, 2282.50)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-400.28, 619.27, 2317.56)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-399.44, 623.40, 2428.98)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-402.19, 623.35, 2647.35)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-401.05, 623.35, 2668.95)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-400.46, 623.37, 3160.63)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-416.71, 625.16, 3159.21)) then stageCompleted = false end

                elseif SelectedStageWorld2 == "Stage 9" then
                    deleteStage2Walls()
                    deleteStage3Lava()
                    deleteMacaronMonster()
                    deleteTwomps()
                    deleteStage8Fans()
                    if not moveTo(Vector3.new(-398.16, 500.11, 188.13)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-395.72, 500.11, 434.25)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-350.24, 505.36, 496.19)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-349.6, 527.04, 574.06)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-452.35, 527.04, 576.82)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-453.93, 554.04, 476.95)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-345.24, 554.04, 465.28)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-344.94, 581.11, 566.84)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-450.11, 581.11, 571.72)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-451.41, 608.04, 479.18)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-397.3, 608.11, 466.75)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-399.83, 607.9, 606.59)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-398.61, 607.9, 613.41)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-400.06, 607.46, 840.18)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-399.14, 607.46, 854.64)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-398.53, 607.46, 1264.53)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-393.12, 607.58, 1297.70)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-394.29, 618.99, 1332.32)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-402.27, 607.46, 1430.33)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-391.79, 607.47, 1477.06)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-365.01, 628.25, 1543.08)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-362.88, 605.34, 1755.79)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-363.98, 617.18, 1792.75)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-389.76, 607.46, 1859.04)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-405.53, 607.91, 1924.72)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-404.55, 619.52, 1960.03)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-406.62, 607.46, 2037.65)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-403.46, 607.56, 2104.56)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-400.69, 619.33, 2140.17)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-403.92, 607.46, 2214.57)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-400.68, 607.67, 2282.50)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-400.28, 619.27, 2317.56)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-399.44, 623.40, 2428.98)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-402.19, 623.35, 2647.35)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-401.05, 623.35, 2668.95)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-400.46, 623.37, 3160.63)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-400.91, 623.37, 3313.76)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-214.55, 623.37, 3331.92)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-96.55, 623.37, 3247.45)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-108.83, 623.37, 3437.81)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-249.50, 623.37, 3441.24)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-524.05, 623.37, 3605.81)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-543.10, 623.37, 3779.61)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-131.91, 623.37, 3790.02)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-116.41, 623.37, 3863.16)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-65.28, 623.44, 3863.15)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-57.90, 625.16, 3882.94)) then stageCompleted = false end

                elseif SelectedStageWorld2 == "Stage 10" then
                    deleteStage2Walls()
                    deleteStage3Lava()
                    deleteMacaronMonster()
                    deleteTwomps()
                    deleteStage8Fans()
                    if not moveTo(Vector3.new(-398.16, 500.11, 188.13)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-395.72, 500.11, 434.25)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-350.24, 505.36, 496.19)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-349.6, 527.04, 574.06)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-452.35, 527.04, 576.82)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-453.93, 554.04, 476.95)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-345.24, 554.04, 465.28)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-344.94, 581.11, 566.84)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-450.11, 581.11, 571.72)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-451.41, 608.04, 479.18)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-397.3, 608.11, 466.75)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-399.83, 607.9, 606.59)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-398.61, 607.9, 613.41)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-400.06, 607.46, 840.18)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-399.14, 607.46, 854.64)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-398.53, 607.46, 1264.53)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-393.12, 607.58, 1297.70)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-394.29, 618.99, 1332.32)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-402.27, 607.46, 1430.33)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-391.77, 607.47, 1477.06)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-365.01, 628.25, 1543.08)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-362.88, 605.34, 1755.79)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-363.98, 617.18, 1792.75)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-389.76, 607.46, 1859.04)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-405.53, 607.91, 1924.72)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-404.55, 619.52, 1960.03)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-406.62, 607.46, 2037.65)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-403.46, 607.56, 2104.56)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-400.69, 619.33, 2140.17)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-403.92, 607.46, 2214.57)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-400.68, 607.67, 2282.50)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-400.28, 619.27, 2317.56)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-399.44, 623.40, 2428.98)) then stageCompleted = false end

                    local world2 = workspace:FindFirstChild("WORLD 2")
                    local stage10 = world2 and world2:FindFirstChild("Stage10")

                    if stageCompleted and stage10 then
                        local wall1 = stage10:FindFirstChild("DoorWall1")
                        local data1 = {
                            GreenDoorKillPart = {Vector3.new(347.15, 623.77, 3836.63), Vector3.new(383.46, 623.94, 3834.3)},
                            RedDoorKillPart = {Vector3.new(355.28, 623.55, 3866.35), Vector3.new(382.66, 623.56, 3866.39)},
                            YellowDoorKillPart = {Vector3.new(357.02, 623.78, 3897.35), Vector3.new(383.59, 623.91, 3896.14)}
                        }
                        if not passStage10Doors(wall1, data1) then stageCompleted = false end
                    else
                        stageCompleted = false
                    end

                    if stageCompleted and stage10 then
                        local wall2 = stage10:FindFirstChild("DoorWall2")
                        local data2 = {
                            GreenDoorKillPart = {Vector3.new(747.18, 623.95, 3834.22), Vector3.new(797.73, 623.87, 3840.25)},
                            RedDoorKillPart = {Vector3.new(756.48, 623.55, 3869.3), Vector3.new(793.8, 623.58, 3868.76)},
                            YellowDoorKillPart = {Vector3.new(760.71, 623.77, 3897.02), Vector3.new(794.09, 623.92, 3897.52)}
                        }
                        if not passStage10Doors(wall2, data2) then stageCompleted = false end
                    else
                        stageCompleted = false
                    end

                    if stageCompleted and stage10 then
                        local wall3 = stage10:FindFirstChild("DoorWall3")
                        local data3 = {
                            GreenDoorKillPart = {Vector3.new(1160.04, 623.83, 3836.53), Vector3.new(1200.44, 623.64, 3835.31)},
                            RedDoorKillPart = {Vector3.new(1163.65, 623.55, 3866.69), Vector3.new(1197.61, 623.64, 3867.5)},
                            YellowDoorKillPart = {Vector3.new(1163.1, 623.75, 3896.18), Vector3.new(1199.25, 623.64, 3897.12)}
                        }
                        if not passStage10Doors(wall3, data3) then stageCompleted = false end
                    else
                        stageCompleted = false
                    end

                    if stageCompleted then
                        if not moveTo(Vector3.new(1218.23, 623.64, 3868.37)) then stageCompleted = false end
                        if stageCompleted and not moveTo(Vector3.new(1230.49, 625.53, 3909.55)) then stageCompleted = false end
                    end

                elseif SelectedStageWorld2 == "Stage 11" then
                    deleteStage2Walls()
                    deleteStage3Lava()
                    deleteMacaronMonster()
                    deleteTwomps()
                    deleteStage8Fans()
                    if not moveTo(Vector3.new(-398.16, 500.11, 188.13)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-395.72, 500.11, 434.25)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-350.24, 505.36, 496.19)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-349.6, 527.04, 574.06)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-452.35, 527.04, 576.82)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-453.93, 554.04, 476.95)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-345.24, 554.04, 465.28)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-344.94, 581.11, 566.84)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-450.11, 581.11, 571.72)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-451.41, 608.04, 479.18)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-397.3, 608.11, 466.75)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-399.83, 607.9, 606.59)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-398.61, 607.9, 613.41)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-400.06, 607.46, 840.18)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-399.14, 607.46, 854.64)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-398.53, 607.46, 1264.53)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-393.12, 607.58, 1297.70)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-394.29, 618.99, 1332.32)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-402.27, 607.46, 1430.33)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-391.79, 607.47, 1477.06)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-365.01, 628.25, 1543.08)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-362.88, 605.34, 1755.79)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-363.98, 617.18, 1792.75)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-389.76, 607.46, 1859.04)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-405.53, 607.91, 1924.72)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-404.55, 619.52, 1960.03)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-406.62, 607.46, 2037.65)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-403.46, 607.56, 2104.56)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-400.69, 619.33, 2140.17)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-403.92, 607.46, 2214.57)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-400.68, 607.67, 2282.50)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-400.28, 619.27, 2317.56)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-399.44, 623.40, 2428.98)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-402.19, 623.35, 2647.35)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-401.05, 623.35, 2668.95)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-400.46, 623.37, 3160.63)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-400.91, 623.37, 3313.76)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-214.55, 623.37, 3331.92)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-96.55, 623.37, 3247.45)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-108.83, 623.37, 3437.81)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-249.50, 623.37, 3441.24)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-524.05, 623.37, 3605.81)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-543.10, 623.37, 3779.61)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-131.91, 623.37, 3790.02)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-116.41, 623.37, 3863.16)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-65.28, 623.44, 3863.15)) then stageCompleted = false end

                    local world2b = workspace:FindFirstChild("WORLD 2")
                    local stage10b = world2b and world2b:FindFirstChild("Stage10")

                    if stageCompleted and stage10b then
                        local wall1 = stage10b:FindFirstChild("DoorWall1")
                        local data1 = {
                            GreenDoorKillPart = {Vector3.new(347.15, 623.77, 3836.63), Vector3.new(383.46, 623.94, 3834.3)},
                            RedDoorKillPart = {Vector3.new(355.28, 623.55, 3866.35), Vector3.new(382.66, 623.56, 3866.39)},
                            YellowDoorKillPart = {Vector3.new(357.02, 623.78, 3897.35), Vector3.new(383.59, 623.91, 3896.14)}
                        }
                        if not passStage10Doors(wall1, data1) then stageCompleted = false end
                    else
                        stageCompleted = false
                    end

                    if stageCompleted and stage10b then
                        local wall2 = stage10b:FindFirstChild("DoorWall2")
                        local data2 = {
                            GreenDoorKillPart = {Vector3.new(747.18, 623.95, 3834.22), Vector3.new(797.73, 623.87, 3840.25)},
                            RedDoorKillPart = {Vector3.new(756.48, 623.55, 3869.3), Vector3.new(793.8, 623.58, 3868.76)},
                            YellowDoorKillPart = {Vector3.new(760.71, 623.77, 3897.02), Vector3.new(794.09, 623.92, 3897.52)}
                        }
                        if not passStage10Doors(wall2, data2) then stageCompleted = false end
                    else
                        stageCompleted = false
                    end

                    if stageCompleted and stage10b then
                        local wall3 = stage10b:FindFirstChild("DoorWall3")
                        local data3 = {
                            GreenDoorKillPart = {Vector3.new(1160.04, 623.83, 3836.53), Vector3.new(1200.44, 623.64, 3835.31)},
                            RedDoorKillPart = {Vector3.new(1163.65, 623.55, 3866.69), Vector3.new(1197.61, 623.64, 3867.5)},
                            YellowDoorKillPart = {Vector3.new(1163.1, 623.75, 3896.18), Vector3.new(1199.25, 623.64, 3897.12)}
                        }
                        if not passStage10Doors(wall3, data3) then stageCompleted = false end
                    else
                        stageCompleted = false
                    end

                    if stageCompleted then
                        if not moveTo(Vector3.new(1218.23, 623.64, 3868.37)) then stageCompleted = false end
                    end

                    if stageCompleted and not moveTo(Vector3.new(1297.19, 623.68, 3867.01)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(1327.56, 620.58, 3858.81)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(1548.2, 631.48, 3793.69)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(1754.7, 639.42, 3951.41)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(1959.48, 634.81, 3790.83)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(2095.47, 638.19, 3973.43)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(2299.33, 626.48, 3865.17)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(2399.35, 627.64, 3871.94)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(2399.91, 629.47, 3887.29)) then stageCompleted = false end

                elseif SelectedStageWorld2 == "Stage 12" then
                    deleteStage2Walls()
                    deleteStage3Lava()
                    deleteMacaronMonster()
                    deleteTwomps()
                    deleteStage8Fans()
                    if not moveTo(Vector3.new(-398.16, 500.11, 188.13)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-395.72, 500.11, 434.25)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-350.24, 505.36, 496.19)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-349.6, 527.04, 574.06)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-452.35, 527.04, 576.82)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-453.93, 554.04, 476.95)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-345.24, 554.04, 465.28)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-344.94, 581.11, 566.84)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-450.11, 581.11, 571.72)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-451.41, 608.04, 479.18)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-397.3, 608.11, 466.75)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-399.83, 607.9, 606.59)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-398.61, 607.9, 613.41)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-400.06, 607.46, 840.18)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-399.14, 607.46, 854.64)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-398.53, 607.46, 1264.53)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-393.12, 607.58, 1297.70)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-394.29, 618.99, 1332.32)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-402.27, 607.46, 1430.33)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-391.79, 607.47, 1477.06)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-365.01, 628.25, 1543.08)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-362.88, 605.34, 1755.79)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-363.98, 617.18, 1792.75)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-389.76, 607.46, 1859.04)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-405.53, 607.91, 1924.72)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-404.55, 619.52, 1960.03)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-406.62, 607.46, 2037.65)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-403.46, 607.56, 2104.56)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-400.69, 619.33, 2140.17)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-403.92, 607.46, 2214.57)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-400.68, 607.67, 2282.50)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-400.28, 619.27, 2317.56)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-399.44, 623.40, 2428.98)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-402.19, 623.35, 2647.35)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-401.05, 623.35, 2668.95)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-400.46, 623.37, 3160.63)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-400.91, 623.37, 3313.76)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-214.55, 623.37, 3331.92)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-96.55, 623.37, 3247.45)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-108.83, 623.37, 3437.81)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-249.50, 623.37, 3441.24)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-524.05, 623.37, 3605.81)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-543.10, 623.37, 3779.61)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-131.91, 623.37, 3790.02)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-116.41, 623.37, 3863.16)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-65.28, 623.44, 3863.15)) then stageCompleted = false end

                    local world2c = workspace:FindFirstChild("WORLD 2")
                    local stage10c = world2c and world2c:FindFirstChild("Stage10")

                    if stageCompleted and stage10c then
                        local wall1 = stage10c:FindFirstChild("DoorWall1")
                        local data1 = {
                            GreenDoorKillPart = {Vector3.new(347.15, 623.77, 3836.63), Vector3.new(383.46, 623.94, 3834.3)},
                            RedDoorKillPart = {Vector3.new(355.28, 623.55, 3866.35), Vector3.new(382.66, 623.56, 3866.39)},
                            YellowDoorKillPart = {Vector3.new(357.02, 623.78, 3897.35), Vector3.new(383.59, 623.91, 3896.14)}
                        }
                        if not passStage10Doors(wall1, data1) then stageCompleted = false end
                    else
                        stageCompleted = false
                    end

                    if stageCompleted and stage10c then
                        local wall2 = stage10c:FindFirstChild("DoorWall2")
                        local data2 = {
                            GreenDoorKillPart = {Vector3.new(747.18, 623.95, 3834.22), Vector3.new(797.73, 623.87, 3840.25)},
                            RedDoorKillPart = {Vector3.new(756.48, 623.55, 3869.3), Vector3.new(793.8, 623.58, 3868.76)},
                            YellowDoorKillPart = {Vector3.new(760.71, 623.77, 3897.02), Vector3.new(794.09, 623.92, 3897.52)}
                        }
                        if not passStage10Doors(wall2, data2) then stageCompleted = false end
                    else
                        stageCompleted = false
                    end

                    if stageCompleted and stage10c then
                        local wall3 = stage10c:FindFirstChild("DoorWall3")
                        local data3 = {
                            GreenDoorKillPart = {Vector3.new(1160.04, 623.83, 3836.53), Vector3.new(1200.44, 623.64, 3835.31)},
                            RedDoorKillPart = {Vector3.new(1163.65, 623.55, 3866.69), Vector3.new(1197.61, 623.64, 3867.5)},
                            YellowDoorKillPart = {Vector3.new(1163.1, 623.75, 3896.18), Vector3.new(1199.25, 623.64, 3897.12)}
                        }
                        if not passStage10Doors(wall3, data3) then stageCompleted = false end
                    else
                        stageCompleted = false
                    end

                    if stageCompleted then
                        if not moveTo(Vector3.new(1218.23, 623.64, 3868.37)) then stageCompleted = false end
                    end

                    if stageCompleted and not moveTo(Vector3.new(1297.19, 623.68, 3867.01)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(1327.56, 620.58, 3858.81)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(1548.2, 631.48, 3793.69)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(1754.7, 639.42, 3951.41)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(1959.48, 634.81, 3790.83)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(2095.47, 638.19, 3973.43)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(2299.33, 626.48, 3865.17)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(2399.35, 627.64, 3871.94)) then stageCompleted = false end

                    if stageCompleted and not moveTo(Vector3.new(2453.97, 627.58, 3871.32)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(2494.39, 639.57, 3873.13)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(2549.86, 639.57, 3868.54)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(2661.09, 634.57, 3869.48)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(2725.85, 634.57, 3871.69)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(2734.56, 628.33, 3871.58)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(2734.56, 575.57, 3871.58)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(2834.25, 575.58, 3872.65)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(2864.99, 582.27, 3872.95)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(2893.92, 597.75, 3875.94)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(2919.22, 605.31, 3876.71)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(2956.19, 576.55, 3877.27)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(3006.13, 576.55, 3877.48)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(3049.43, 592.03, 3876.84)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(3173.56, 578.34, 3878.62)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(3215.76, 592.55, 3877.01)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(3268.21, 592.57, 3867.54)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(3270.38, 594.57, 3889.75)) then stageCompleted = false end

                elseif SelectedStageWorld2 == "Stage 13" then
                    deleteStage2Walls()
                    deleteStage3Lava()
                    deleteMacaronMonster()
                    deleteTwomps()
                    deleteStage8Fans()
                    if not moveTo(Vector3.new(-398.16, 500.11, 188.13)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-395.72, 500.11, 434.25)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-350.24, 505.36, 496.19)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-349.6, 527.04, 574.06)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-452.35, 527.04, 576.82)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-453.93, 554.04, 476.95)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-345.24, 554.04, 465.28)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-344.94, 581.11, 566.84)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-450.11, 581.11, 571.72)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-451.41, 608.04, 479.18)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-397.3, 608.11, 466.75)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-399.83, 607.9, 606.59)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-398.61, 607.9, 613.41)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-400.06, 607.46, 840.18)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-399.14, 607.46, 854.64)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-398.53, 607.46, 1264.53)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-393.12, 607.58, 1297.70)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-394.29, 618.99, 1332.32)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-402.27, 607.46, 1430.33)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-391.79, 607.47, 1477.06)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-365.01, 628.25, 1543.08)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-362.88, 605.34, 1755.79)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-363.98, 617.18, 1792.75)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-389.76, 607.46, 1859.04)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-405.53, 607.91, 1924.72)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-404.55, 619.52, 1960.03)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-406.62, 607.46, 2037.65)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-403.46, 607.56, 2104.56)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-400.69, 619.33, 2140.17)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-403.92, 607.46, 2214.57)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-400.68, 607.67, 2282.50)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-400.28, 619.27, 2317.56)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-399.44, 623.40, 2428.98)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-402.19, 623.35, 2647.35)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-401.05, 623.35, 2668.95)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-400.46, 623.37, 3160.63)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-400.91, 623.37, 3313.76)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-214.55, 623.37, 3331.92)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-96.55, 623.37, 3247.45)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-108.83, 623.37, 3437.81)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-249.50, 623.37, 3441.24)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-524.05, 623.37, 3605.81)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-543.10, 623.37, 3779.61)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-131.91, 623.37, 3790.02)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-116.41, 623.37, 3863.16)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(-65.28, 623.44, 3863.15)) then stageCompleted = false end

                    local world2d = workspace:FindFirstChild("WORLD 2")
                    local stage10d = world2d and world2d:FindFirstChild("Stage10")

                    if stageCompleted and stage10d then
                        local wall1 = stage10d:FindFirstChild("DoorWall1")
                        local data1 = {
                            GreenDoorKillPart = {Vector3.new(347.15, 623.77, 3836.63), Vector3.new(383.46, 623.94, 3834.3)},
                            RedDoorKillPart = {Vector3.new(355.28, 623.55, 3866.35), Vector3.new(382.66, 623.56, 3866.39)},
                            YellowDoorKillPart = {Vector3.new(357.02, 623.78, 3897.35), Vector3.new(383.59, 623.91, 3896.14)}
                        }
                        if not passStage10Doors(wall1, data1) then stageCompleted = false end
                    else
                        stageCompleted = false
                    end

                    if stageCompleted and stage10d then
                        local wall2 = stage10d:FindFirstChild("DoorWall2")
                        local data2 = {
                            GreenDoorKillPart = {Vector3.new(747.18, 623.95, 3834.22), Vector3.new(797.73, 623.87, 3840.25)},
                            RedDoorKillPart = {Vector3.new(756.48, 623.55, 3869.3), Vector3.new(793.8, 623.58, 3868.76)},
                            YellowDoorKillPart = {Vector3.new(760.71, 623.77, 3897.02), Vector3.new(794.09, 623.92, 3897.52)}
                        }
                        if not passStage10Doors(wall2, data2) then stageCompleted = false end
                    else
                        stageCompleted = false
                    end

                    if stageCompleted and stage10d then
                        local wall3 = stage10d:FindFirstChild("DoorWall3")
                        local data3 = {
                            GreenDoorKillPart = {Vector3.new(1160.04, 623.83, 3836.53), Vector3.new(1200.44, 623.64, 3835.31)},
                            RedDoorKillPart = {Vector3.new(1163.65, 623.55, 3866.69), Vector3.new(1197.61, 623.64, 3867.5)},
                            YellowDoorKillPart = {Vector3.new(1163.1, 623.75, 3896.18), Vector3.new(1199.25, 623.64, 3897.12)}
                        }
                        if not passStage10Doors(wall3, data3) then stageCompleted = false end
                    else
                        stageCompleted = false
                    end

                    if stageCompleted then
                        if not moveTo(Vector3.new(1218.23, 623.64, 3868.37)) then stageCompleted = false end
                    end

                    if stageCompleted and not moveTo(Vector3.new(1297.19, 623.68, 3867.01)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(1327.56, 620.58, 3858.81)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(1548.2, 631.48, 3793.69)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(1754.7, 639.42, 3951.41)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(1959.48, 634.81, 3790.83)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(2095.47, 638.19, 3973.43)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(2299.33, 626.48, 3865.17)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(2399.35, 627.64, 3871.94)) then stageCompleted = false end

                    if stageCompleted and not moveTo(Vector3.new(2453.97, 627.58, 3871.32)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(2494.39, 639.57, 3873.13)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(2549.86, 639.57, 3868.54)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(2661.09, 634.57, 3869.48)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(2725.85, 634.57, 3871.69)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(2734.56, 628.33, 3871.58)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(2734.56, 575.57, 3871.58)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(2834.25, 575.58, 3872.65)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(2864.99, 582.27, 3872.95)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(2893.92, 597.75, 3875.94)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(2919.22, 605.31, 3876.71)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(2956.19, 576.55, 3877.27)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(3006.13, 576.55, 3877.48)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(3049.43, 592.03, 3876.84)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(3173.56, 578.34, 3878.62)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(3215.76, 592.55, 3877.01)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(3268.21, 592.57, 3867.54)) then stageCompleted = false end

                    if stageCompleted and not moveTo(Vector3.new(3289.06, 592.57, 3872.89)) then stageCompleted = false end

                    if stageCompleted then
                        local npcPiege = workspace:FindFirstChild("NPC & Piege")
                        local tsunami = npcPiege and npcPiege:FindFirstChild("Tsunami1")
                        if tsunami then
                            while AutofarmWorld2 and (tsunami:GetPivot().Position - rootPart.Position).Magnitude < 70 do
                                task.wait(0.05)
                            end
                        end
                    end

                    if stageCompleted and not moveTo(Vector3.new(3347.35, 567.66, 3874.85)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(3400, 567.66, 4000)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(3420, 567.66, 4500)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(3400, 567.66, 5000)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(3324.92, 567.66, 5064.37)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(3315.32, 567.66, 5147)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(4633.99, 567.68, 5138.21)) then stageCompleted = false end
                    if stageCompleted and not moveTo(Vector3.new(4633.9, 569.68, 5163.07)) then stageCompleted = false end
                end

                if stageCompleted and AutofarmWorld2 then
                    task.wait(1)
                    local char = LocalPlayer.Character
                    if char then
                        local hum = char:FindFirstChild("Humanoid")
                        if hum then
                            hum:ChangeState(Enum.HumanoidStateType.Jumping)
                        end
                    end
                    task.wait(2)
                end
            else
                LocalPlayer.CharacterAdded:Wait()
                task.wait(1)
            end
        end
    end
end)

Library:Toggle(true)

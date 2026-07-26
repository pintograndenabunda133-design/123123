-- ============================================================
--  MENU PROFISSIONAL PARA ROBLOX
--  Features: Aimbot, Skeleton ESP, Team Check
--  Design: Clean, Modern, Minimalista
-- ============================================================

local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local UserInputService = game:GetService("UserInputService")
local StarterGui = game:GetService("StarterGui")
local ContextActionService = game:GetService("ContextActionService")

local player = Players.LocalPlayer
local camera = workspace.CurrentCamera

-- ============================================================
--  CONFIGURAÇÕES PADRÃO
-- ============================================================

local Config = {
    aimbot = {
        enabled = false,
        smoothness = 3,
        fov = 100,
        silent = false,
        targetTeam = true,
        keybind = Enum.UserInputType.MouseButton2,
        prediction = 0.165,
        targetPart = "Head", -- "Head", "Torso", "RootPart"
        headOffset = 2.0, -- Altura da mira em studs acima da RootPart
    },
    esp = {
        enabled = false,
        skeleton = true,
        names = true,
        health = true,
        distance = true,
        tracers = true,
        color = Color3.fromRGB(255, 50, 50),
        teamColor = true,
        showTeam = true,
    },
    teamCheck = {
        enabled = true,
    },
}

-- ============================================================
--  GUI SETUP - MENU CLEAN E PROFISSIONAL
-- ============================================================

local oldGui = player.PlayerGui:FindFirstChild("ProMenu")
if oldGui then oldGui:Destroy() end

local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Name = "ProMenu"
ScreenGui.ResetOnSpawn = false
ScreenGui.ZIndexBehavior = Enum.ZIndexBehavior.Sibling
ScreenGui.DisplayOrder = 999
ScreenGui.IgnoreGuiInset = true
ScreenGui.OnTopOfCoreBlur = true
ScreenGui.Enabled = true
ScreenGui.Parent = player.PlayerGui

local mainFrame = Instance.new("Frame")
mainFrame.Name = "MainFrame"
mainFrame.Size = UDim2.new(0, 340, 0, 480)
mainFrame.Position = UDim2.new(0.5, -170, 0.5, -240)
mainFrame.BackgroundColor3 = Color3.fromRGB(18, 18, 24)
mainFrame.BorderSizePixel = 0
mainFrame.ClipsDescendants = true
mainFrame.Parent = ScreenGui

local innerBorder = Instance.new("Frame")
innerBorder.Size = UDim2.new(1, 0, 1, 0)
innerBorder.Position = UDim2.new(0, 1, 0, 1)
innerBorder.BackgroundColor3 = Color3.fromRGB(28, 28, 36)
innerBorder.BorderSizePixel = 0
innerBorder.Parent = mainFrame

local titleBar = Instance.new("Frame")
titleBar.Name = "TitleBar"
titleBar.Size = UDim2.new(1, 0, 0, 45)
titleBar.BackgroundColor3 = Color3.fromRGB(22, 22, 30)
titleBar.BorderSizePixel = 0
titleBar.Parent = mainFrame

local accentLine = Instance.new("Frame")
accentLine.Name = "AccentLine"
accentLine.Size = UDim2.new(1, 0, 0, 2)
accentLine.BackgroundColor3 = Color3.fromRGB(99, 102, 241)
accentLine.BorderSizePixel = 0
accentLine.Parent = titleBar

local titleLabel = Instance.new("TextLabel")
titleLabel.Name = "Title"
titleLabel.Size = UDim2.new(1, -70, 0, 45)
titleLabel.BackgroundTransparency = 1
titleLabel.Text = "  PRO MENU"
titleLabel.TextColor3 = Color3.fromRGB(255, 255, 255)
titleLabel.TextSize = 14
titleLabel.Font = Enum.Font.GothamBold
titleLabel.TextXAlignment = Enum.TextXAlignment.Left
titleLabel.Parent = titleBar

local minimizeBtn = Instance.new("TextButton")
minimizeBtn.Name = "MinimizeBtn"
minimizeBtn.Size = UDim2.new(0, 30, 0, 30)
minimizeBtn.Position = UDim2.new(1, -35, 0, 8)
minimizeBtn.BackgroundColor3 = Color3.fromRGB(35, 35, 45)
minimizeBtn.BorderSizePixel = 0
minimizeBtn.Text = "−"
minimizeBtn.TextColor3 = Color3.fromRGB(160, 160, 180)
minimizeBtn.TextSize = 16
minimizeBtn.Font = Enum.Font.GothamBold
minimizeBtn.Parent = titleBar

local closeBtn = Instance.new("TextButton")
closeBtn.Name = "CloseBtn"
closeBtn.Size = UDim2.new(0, 30, 0, 30)
closeBtn.Position = UDim2.new(1, -65, 0, 8)
closeBtn.BackgroundColor3 = Color3.fromRGB(35, 35, 45)
closeBtn.BorderSizePixel = 0
closeBtn.Text = "×"
closeBtn.TextColor3 = Color3.fromRGB(160, 160, 180)
closeBtn.TextSize = 16
closeBtn.Font = Enum.Font.GothamBold
closeBtn.Parent = titleBar

local contentFrame = Instance.new("ScrollingFrame")
contentFrame.Name = "Content"
contentFrame.Size = UDim2.new(1, -16, 1, -60)
contentFrame.Position = UDim2.new(0, 8, 0, 50)
contentFrame.BackgroundColor3 = Color3.fromRGB(18, 18, 24)
contentFrame.BorderSizePixel = 0
contentFrame.ScrollBarThickness = 3
contentFrame.ScrollBarImageColor3 = Color3.fromRGB(99, 102, 241)
contentFrame.CanvasSize = UDim2.new(0, 0, 0, 0)
contentFrame.AutomaticCanvasSize = Enum.AutomaticSize.Y
contentFrame.Parent = mainFrame

-- ============================================================
--  FUNÇÕES AUXILIARES PARA COMPONENTES
-- ============================================================

local function createSectionTitle(parent, text)
    local frame = Instance.new("Frame")
    frame.Size = UDim2.new(1, -16, 0, 30)
    frame.BackgroundTransparency = 1
    frame.Parent = parent

    local label = Instance.new("TextLabel")
    label.Size = UDim2.new(1, 0, 1, 0)
    label.BackgroundTransparency = 1
    label.Text = " " .. text
    label.TextColor3 = Color3.fromRGB(130, 130, 160)
    label.TextSize = 11
    label.Font = Enum.Font.GothamMedium
    label.TextXAlignment = Enum.TextXAlignment.Left
    label.Parent = frame
end

local function createToggle(parent, name, defaultValue, callback)
    local frame = Instance.new("Frame")
    frame.Size = UDim2.new(1, -16, 0, 42)
    frame.BackgroundColor3 = Color3.fromRGB(24, 24, 32)
    frame.BorderSizePixel = 0
    frame.Parent = parent

    local label = Instance.new("TextLabel")
    label.Size = UDim2.new(1, -60, 1, 0)
    label.Position = UDim2.new(0, 12, 0, 0)
    label.BackgroundTransparency = 1
    label.Text = name
    label.TextColor3 = Color3.fromRGB(200, 200, 220)
    label.TextSize = 13
    label.Font = Enum.Font.GothamMedium
    label.TextXAlignment = Enum.TextXAlignment.Left
    label.Parent = frame

    local toggleBg = Instance.new("Frame")
    toggleBg.Size = UDim2.new(0, 44, 0, 24)
    toggleBg.Position = UDim2.new(1, -52, 0.5, -12)
    toggleBg.BackgroundColor3 = defaultValue and Color3.fromRGB(99, 102, 241) or Color3.fromRGB(45, 45, 55)
    toggleBg.BorderSizePixel = 0
    toggleBg.Parent = frame

    local toggleCorner = Instance.new("UICorner")
    toggleCorner.CornerRadius = UDim.new(0, 12)
    toggleCorner.Parent = toggleBg

    local knob = Instance.new("Frame")
    knob.Size = UDim2.new(0, 18, 0, 18)
    knob.Position = defaultValue and UDim2.new(1, -21, 0.5, -9) or UDim2.new(0, 3, 0.5, -9)
    knob.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
    knob.BorderSizePixel = 0
    knob.Parent = toggleBg

    local knobCorner = Instance.new("UICorner")
    knobCorner.CornerRadius = UDim.new(0, 9)
    knobCorner.Parent = knob

    toggleBg.InputBegan:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1 then
            defaultValue = not defaultValue
            toggleBg.BackgroundColor3 = defaultValue and Color3.fromRGB(99, 102, 241) or Color3.fromRGB(45, 45, 55)
            local targetPos = defaultValue and UDim2.new(1, -21, 0.5, -9) or UDim2.new(0, 3, 0.5, -9)
            knob:TweenPosition(targetPos, Enum.EasingDirection.Out, Enum.EasingStyle.Quad, 0.15, true)
            if callback then callback(defaultValue) end
        end
    end)

    return toggleBg
end

local function createSlider(parent, name, minVal, maxVal, defaultVal, step, callback)
    local frame = Instance.new("Frame")
    frame.Size = UDim2.new(1, -16, 0, 56)
    frame.BackgroundColor3 = Color3.fromRGB(24, 24, 32)
    frame.BorderSizePixel = 0
    frame.Parent = parent

    local label = Instance.new("TextLabel")
    label.Size = UDim2.new(1, -60, 0, 18)
    label.Position = UDim2.new(0, 12, 0, 6)
    label.BackgroundTransparency = 1
    label.Text = name
    label.TextColor3 = Color3.fromRGB(200, 200, 220)
    label.TextSize = 13
    label.Font = Enum.Font.GothamMedium
    label.TextXAlignment = Enum.TextXAlignment.Left
    label.Parent = frame

    local valueLabel = Instance.new("TextLabel")
    valueLabel.Name = "ValueLabel"
    valueLabel.Size = UDim2.new(0, 50, 0, 18)
    valueLabel.Position = UDim2.new(1, -62, 0, 6)
    valueLabel.BackgroundTransparency = 1
    valueLabel.Text = tostring(defaultVal)
    valueLabel.TextColor3 = Color3.fromRGB(160, 160, 200)
    valueLabel.TextSize = 12
    valueLabel.Font = Enum.Font.GothamMedium
    valueLabel.TextXAlignment = Enum.TextXAlignment.Right
    valueLabel.Parent = frame

    local sliderBg = Instance.new("Frame")
    sliderBg.Name = "SliderBg"
    sliderBg.Size = UDim2.new(1, -24, 0, 6)
    sliderBg.Position = UDim2.new(0, 12, 0, 32)
    sliderBg.BackgroundColor3 = Color3.fromRGB(45, 45, 55)
    sliderBg.BorderSizePixel = 0
    sliderBg.Parent = frame

    local sliderCorner = Instance.new("UICorner")
    sliderCorner.CornerRadius = UDim.new(0, 3)
    sliderCorner.Parent = sliderBg

    local sliderFill = Instance.new("Frame")
    sliderFill.Name = "SliderFill"
    local ratio = (defaultVal - minVal) / (maxVal - minVal)
    sliderFill.Size = UDim2.new(ratio, 0, 1, 0)
    sliderFill.BackgroundColor3 = Color3.fromRGB(99, 102, 241)
    sliderFill.BorderSizePixel = 0
    sliderFill.Parent = sliderBg

    local fillCorner = Instance.new("UICorner")
    fillCorner.CornerRadius = UDim.new(0, 3)
    fillCorner.Parent = sliderFill

    local knob = Instance.new("Frame")
    knob.Size = UDim2.new(0, 14, 0, 14)
    knob.Position = UDim2.new(ratio, -7, 0.5, -7)
    knob.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
    knob.BorderSizePixel = 0
    knob.ZIndex = 2
    knob.Parent = sliderBg

    local knobCorner = Instance.new("UICorner")
    knobCorner.CornerRadius = UDim.new(0, 7)
    knobCorner.Parent = knob

    local dragging = false
    sliderBg.InputBegan:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1 then
            dragging = true
        end
    end)

    UserInputService.InputEnded:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1 then
            dragging = false
        end
    end)

    UserInputService.InputChanged:Connect(function(input)
        if dragging and input.UserInputType == Enum.UserInputType.MouseMovement then
            local relativeX = (input.Position.X - sliderBg.AbsolutePosition.X) / sliderBg.AbsoluteSize.X
            relativeX = math.clamp(relativeX, 0, 1)
            local newValue = minVal + (maxVal - minVal) * relativeX
            newValue = math.floor(newValue / step + 0.5) * step

            sliderFill.Size = UDim2.new(relativeX, 0, 1, 0)
            knob.Position = UDim2.new(relativeX, -7, 0.5, -7)
            valueLabel.Text = tostring(newValue)

            if callback then callback(newValue) end
        end
    end)

    return frame
end

local function createKeybind(parent, name, defaultKey, callback)
    local frame = Instance.new("Frame")
    frame.Size = UDim2.new(1, -16, 0, 42)
    frame.BackgroundColor3 = Color3.fromRGB(24, 24, 32)
    frame.BorderSizePixel = 0
    frame.Parent = parent

    local label = Instance.new("TextLabel")
    label.Size = UDim2.new(1, -90, 1, 0)
    label.Position = UDim2.new(0, 12, 0, 0)
    label.BackgroundTransparency = 1
    label.Text = name
    label.TextColor3 = Color3.fromRGB(200, 200, 220)
    label.TextSize = 13
    label.Font = Enum.Font.GothamMedium
    label.TextXAlignment = Enum.TextXAlignment.Left
    label.Parent = frame

    local keyButton = Instance.new("TextButton")
    keyButton.Size = UDim2.new(0, 75, 0, 28)
    keyButton.Position = UDim2.new(1, -87, 0.5, -14)
    keyButton.BackgroundColor3 = Color3.fromRGB(35, 35, 48)
    keyButton.BorderSizePixel = 0
    keyButton.Text = "[" .. string.sub(defaultKey.Name, 1, 3) .. "]"
    keyButton.TextColor3 = Color3.fromRGB(160, 160, 200)
    keyButton.TextSize = 11
    keyButton.Font = Enum.Font.GothamBold
    keyButton.Parent = frame

    local keyCorner = Instance.new("UICorner")
    keyCorner.CornerRadius = UDim.new(0, 6)
    keyCorner.Parent = keyButton

    local waiting = false
    keyButton.MouseButton1Click:Connect(function()
        waiting = true
        keyButton.Text = "[...]"
        keyButton.TextColor3 = Color3.fromRGB(255, 180, 50)
    end)

    UserInputService.InputBegan:Connect(function(input)
        if waiting then
            if input.UserInputType == Enum.UserInputType.Keyboard or
               input.UserInputType == Enum.UserInputType.MouseButton1 or
               input.UserInputType == Enum.UserInputType.MouseButton2 then
                defaultKey = input
                keyButton.Text = "[" .. string.sub(input.Name, 1, 3) .. "]"
                keyButton.TextColor3 = Color3.fromRGB(160, 160, 200)
                waiting = false
                if callback then callback(defaultKey) end
            end
        end
    end)
end

local function createDropdown(parent, name, options, defaultIndex, callback)
    local frame = Instance.new("Frame")
    frame.Size = UDim2.new(1, -16, 0, 42)
    frame.BackgroundColor3 = Color3.fromRGB(24, 24, 32)
    frame.BorderSizePixel = 0
    frame.Parent = parent

    local label = Instance.new("TextLabel")
    label.Size = UDim2.new(1, -90, 1, 0)
    label.Position = UDim2.new(0, 12, 0, 0)
    label.BackgroundTransparency = 1
    label.Text = name
    label.TextColor3 = Color3.fromRGB(200, 200, 220)
    label.TextSize = 13
    label.Font = Enum.Font.GothamMedium
    label.TextXAlignment = Enum.TextXAlignment.Left
    label.Parent = frame

    local dropdownBtn = Instance.new("TextButton")
    dropdownBtn.Size = UDim2.new(0, 120, 0, 28)
    dropdownBtn.Position = UDim2.new(1, -132, 0.5, -14)
    dropdownBtn.BackgroundColor3 = Color3.fromRGB(35, 35, 48)
    dropdownBtn.BorderSizePixel = 0
    dropdownBtn.Text = options[defaultIndex] or "..."
    dropdownBtn.TextColor3 = Color3.fromRGB(160, 160, 200)
    dropdownBtn.TextSize = 11
    dropdownBtn.Font = Enum.Font.GothamBold
    dropdownBtn.Parent = frame

    local btnCorner = Instance.new("UICorner")
    btnCorner.CornerRadius = UDim.new(0, 6)
    btnCorner.Parent = dropdownBtn

    local arrowLabel = Instance.new("TextLabel")
    arrowLabel.Size = UDim2.new(0, 20, 0, 28)
    arrowLabel.Position = UDim2.new(1, -24, 0.5, -14)
    arrowLabel.BackgroundTransparency = 1
    arrowLabel.Text = "\u{25BC}"
    arrowLabel.TextColor3 = Color3.fromRGB(160, 160, 200)
    arrowLabel.TextSize = 10
    arrowLabel.Font = Enum.Font.GothamBold
    arrowLabel.Parent = frame

    local currentIdx = defaultIndex

    dropdownBtn.MouseButton1Click:Connect(function()
        currentIdx = currentIdx + 1
        if currentIdx > #options then currentIdx = 1 end
        dropdownBtn.Text = options[currentIdx]
        if callback then callback(currentIdx) end
    end)
end

local function createSpacer(parent)
    local frame = Instance.new("Frame")
    frame.Size = UDim2.new(1, -16, 0, 8)
    frame.BackgroundTransparency = 1
    frame.Parent = parent
end

-- ============================================================
--  MONTAR SEÇÕES DO MENU
-- ============================================================

local layout = Instance.new("UIListLayout")
layout.SortOrder = Enum.SortOrder.LayoutOrder
layout.Padding = UDim.new(0, 4)
layout.Parent = contentFrame

-- SEÇÃO 1: AIMBOT
createSectionTitle(contentFrame, "AIMBOT")

createToggle(contentFrame, "Aimbot Ativo", Config.aimbot.enabled, function(val)
    Config.aimbot.enabled = val
end)

createSlider(contentFrame, "Suavidade", 1, 10, Config.aimbot.smoothness, 1, function(val)
    Config.aimbot.smoothness = val
end)

createSlider(contentFrame, "FOV (Campo de Visão)", 50, 300, Config.aimbot.fov, 10, function(val)
    Config.aimbot.fov = val
end)

-- Target Part Selector
local partNames = {"Cabeça (Head)", "Torso", "Raiz (RootPart)"}
local partValues = {"Head", "Torso", "RootPart"}
local currentPartIndex = 2 -- Head por padrão (indice 1)

for i, pv in ipairs(partValues) do
    if pv == Config.aimbot.targetPart then
        currentPartIndex = i
        break
    end
end

createDropdown(contentFrame, "Mirar em", partNames, currentPartIndex, function(index)
    Config.aimbot.targetPart = partValues[index]
    currentPartIndex = index
end)

createSlider(contentFrame, "Altura da Mira", 1, 4, Config.aimbot.headOffset, 0.1, function(val)
    Config.aimbot.headOffset = val
end)

createToggle(contentFrame, "Aimbot Silencioso", Config.aimbot.silent, function(val)
    Config.aimbot.silent = val
end)

createToggle(contentFrame, "Não Mirar no Time", Config.aimbot.targetTeam, function(val)
    Config.aimbot.targetTeam = val
end)

createKeybind(contentFrame, "Tecla de Mira", Config.aimbot.keybind, function(key)
    Config.aimbot.keybind = key
end)

createSpacer(contentFrame)

-- SEÇÃO 2: ESP
createSectionTitle(contentFrame, "ESP")

createToggle(contentFrame, "ESP Ativo", Config.esp.enabled, function(val)
    Config.esp.enabled = val
end)

createToggle(contentFrame, "Skeleton ESP", Config.esp.skeleton, function(val)
    Config.esp.skeleton = val
end)

createToggle(contentFrame, "Nomes", Config.esp.names, function(val)
    Config.esp.names = val
end)

createToggle(contentFrame, "Barra de Vida", Config.esp.health, function(val)
    Config.esp.health = val
end)

createToggle(contentFrame, "Distância", Config.esp.distance, function(val)
    Config.esp.distance = val
end)

createToggle(contentFrame, "Tracers (Linhas)", Config.esp.tracers, function(val)
    Config.esp.tracers = val
end)

createToggle(contentFrame, "Usar Cor do Time", Config.esp.teamColor, function(val)
    Config.esp.teamColor = val
end)

createToggle(contentFrame, "Mostrar Aliados", Config.esp.showTeam, function(val)
    Config.esp.showTeam = val
end)

createSpacer(contentFrame)

-- SEÇÃO 3: TEAM CHECK
createSectionTitle(contentFrame, "TEAM CHECK")

createToggle(contentFrame, "Team Check Ativo", Config.teamCheck.enabled, function(val)
    Config.teamCheck.enabled = val
end)

-- ============================================================
--  SISTEMA DE ARRASTO DO MENU
-- ============================================================

local draggingMenu = false
local dragStart = nil
local startPos = nil

titleBar.InputBegan:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseButton1 then
        draggingMenu = true
        dragStart = input.Position
        startPos = mainFrame.Position
    end
end)

UserInputService.InputEnded:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseButton1 then
        draggingMenu = false
    end
end)

UserInputService.InputChanged:Connect(function(input)
    if draggingMenu and input.UserInputType == Enum.UserInputType.MouseMovement then
        local delta = input.Position - dragStart
        mainFrame.Position = UDim2.new(
            startPos.X.Scale,
            startPos.X.Offset + delta.X,
            startPos.Y.Scale,
            startPos.Y.Offset + delta.Y
        )
    end
end)

-- ============================================================
--  BOTÕES DO MENU
-- ============================================================

minimizeBtn.MouseButton1Click:Connect(function()
    if contentFrame.Visible then
        contentFrame.Visible = false
        minimizeBtn.Text = "+"
        mainFrame.Size = UDim2.new(0, 340, 0, 47)
    else
        contentFrame.Visible = true
        minimizeBtn.Text = "−"
        mainFrame.Size = UDim2.new(0, 340, 0, 480)
    end
end)

closeBtn.MouseButton1Click:Connect(function()
    ScreenGui:Destroy()
end)

-- ============================================================
--  AIMBOT LOGIC
-- ============================================================

-- Pegar a posição fixa do target (sem seguir animação)
local function getTargetPosition(character)
    local rootPart = character:FindFirstChild("HumanoidRootPart")
    if not rootPart then return nil end

    local partName = Config.aimbot.targetPart

    if partName == "Head" then
        -- Usar RootPart + offset configurável pra cima (altura da cabeça)
        return rootPart.Position + Vector3.new(0, Config.aimbot.headOffset, 0)
    elseif partName == "Torso" then
        -- Usar RootPart como base (já é o torso)
        return rootPart.Position
    elseif partName == "RootPart" then
        return rootPart.Position
    end

    return rootPart.Position
end

local function getClosestPlayer(mousePos)
    local closest = nil
    local closestDist = Config.aimbot.fov

    for _, target in pairs(Players:GetPlayers()) do
        if target ~= player and target.Character then
            local humanoid = target.Character:FindFirstChild("Humanoid")
            if not humanoid or humanoid.Health <= 0 then continue end
            if Config.aimbot.targetTeam and player.Team and target.Team and player.Team == target.Team then continue end

            local rootPart = target.Character:FindFirstChild("HumanoidRootPart")
            if not rootPart then continue end

            local targetPos = getTargetPosition(target.Character)
            if not targetPos then continue end

            local screenPos, onScreen = camera:WorldToViewportPoint(targetPos)

            if onScreen then
                local dist = (Vector2.new(screenPos.X, screenPos.Y) - Vector2.new(mousePos.X, mousePos.Y)).Magnitude
                if dist < closestDist then
                    closestDist = dist
                    closest = target
                end
            end
        end
    end

    return closest
end

local function isAiming()
    return UserInputService:IsMouseButtonPressed(Config.aimbot.keybind)
end

RunService.RenderStepped:Connect(function()
    if not Config.aimbot.enabled then return end
    if not isAiming() then return end

    local mousePos = UserInputService:GetMouseLocation()
    local target = getClosestPlayer(mousePos)

    if target and target.Character then
        local targetPos = getTargetPosition(target.Character)
        if not targetPos then return end

        local rootPart = target.Character:FindFirstChild("HumanoidRootPart")
        if not rootPart then return end

        local velocity = rootPart.AssemblyLinearVelocity or Vector3.new(0, 0, 0)
        local predictedPos = targetPos + velocity * Config.aimbot.prediction

        if not Config.aimbot.silent then
            local targetCFrame = CFrame.lookAt(camera.CFrame.Position, predictedPos)
            camera.CFrame = camera.CFrame:Lerp(targetCFrame, 1 / Config.aimbot.smoothness)
        end
    end
end)

-- ============================================================
--  ESP LOGIC (SKELETON ESP CORRIGIDO)
-- ============================================================

local espFolder = Instance.new("Folder")
espFolder.Name = "ESP_Folder"
espFolder.Parent = player.PlayerGui

local tracerFolder = Instance.new("Folder")
tracerFolder.Name = "Tracer_Folder"
tracerFolder.Parent = ScreenGui

local espCache = {}

local function getPlayerColor(targetPlayer)
    if Config.esp.teamColor then
        if targetPlayer.TeamColor then
            return targetPlayer.TeamColor.Color
        end
    end
    return Config.esp.color
end

-- Criar BillboardGui (Nome + HP + Distância)
local function setupBillboardGui(targetPlayer)
    local bgui = Instance.new("BillboardGui")
    bgui.Name = "ESP_Gui"
    bgui.Size = UDim2.new(0, 120, 0, 100)
    bgui.StudsOffset = Vector3.new(0, 2.5, 0)
    bgui.AlwaysOnTop = true
    bgui.Enabled = false

    local nameLabel = Instance.new("TextLabel")
    nameLabel.Name = "ESP_Name"
    nameLabel.Size = UDim2.new(1, 0, 0, 16)
    nameLabel.Position = UDim2.new(0, 0, 0, 0)
    nameLabel.BackgroundTransparency = 1
    nameLabel.Text = targetPlayer.Name
    nameLabel.TextColor3 = Color3.fromRGB(255, 255, 255)
    nameLabel.TextSize = 13
    nameLabel.Font = Enum.Font.GothamBold
    nameLabel.TextStrokeTransparency = 0.4
    nameLabel.Parent = bgui

    local hpBg = Instance.new("Frame")
    hpBg.Name = "ESP_HPBg"
    hpBg.Size = UDim2.new(1, 0, 0, 4)
    hpBg.Position = UDim2.new(0, 0, 0, 18)
    hpBg.BackgroundColor3 = Color3.fromRGB(60, 60, 60)
    hpBg.BorderSizePixel = 0
    hpBg.Visible = false
    hpBg.Parent = bgui

    local hpBgCorner = Instance.new("UICorner")
    hpBgCorner.CornerRadius = UDim.new(0, 2)
    hpBgCorner.Parent = hpBg

    local hpFill = Instance.new("Frame")
    hpFill.Name = "ESP_HPFill"
    hpFill.Size = UDim2.new(1, 0, 1, 0)
    hpFill.BackgroundColor3 = Color3.fromRGB(50, 220, 100)
    hpFill.BorderSizePixel = 0
    hpFill.Parent = hpBg

    local hpFillCorner = Instance.new("UICorner")
    hpFillCorner.CornerRadius = UDim.new(0, 2)
    hpFillCorner.Parent = hpFill

    local distLabel = Instance.new("TextLabel")
    distLabel.Name = "ESP_Distance"
    distLabel.Size = UDim2.new(1, 0, 0, 14)
    distLabel.Position = UDim2.new(0, 0, 0, 24)
    distLabel.BackgroundTransparency = 1
    distLabel.Text = ""
    distLabel.TextColor3 = Color3.fromRGB(180, 180, 200)
    distLabel.TextSize = 11
    distLabel.Font = Enum.Font.GothamMedium
    distLabel.TextStrokeTransparency = 0.5
    distLabel.Visible = false
    distLabel.Parent = bgui

    bgui.Parent = espFolder
    return bgui
end

-- Criar linhas do Skeleton ESP como Frames no ScreenGui
local function createSkeletonLines(targetPlayer)
    local skeletonFolder = Instance.new("Folder")
    skeletonFolder.Name = "Skeleton_" .. targetPlayer.Name

    local lineNames = {
        "Neck", "LShoulder", "RShoulder",
        "LElbow", "RElbow",
        "LHand", "RHand",
        "Spine",
        "LHip", "RHip",
        "LKnee", "RKnee",
        "LFoot", "RFoot",
    }

    for _, name in ipairs(lineNames) do
        local line = Instance.new("Frame")
        line.Name = "Line_" .. name
        line.Size = UDim2.new(0, 3, 0, 3)
        line.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
        line.BorderSizePixel = 0
        line.BackgroundTransparency = 0.1
        line.ZIndex = 10
        line.Visible = false
        line.Parent = skeletonFolder
    end

    skeletonFolder.Parent = ScreenGui
    return skeletonFolder
end

-- Tracer
local function setupTracer(targetPlayer)
    local frame = Instance.new("Frame")
    frame.Name = "Tracer_" .. targetPlayer.Name
    frame.Size = UDim2.new(0, 2, 0, 2)
    frame.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
    frame.BorderSizePixel = 0
    frame.BackgroundTransparency = 0.3
    frame.ZIndex = 1
    frame.Parent = tracerFolder

    return frame
end

-- Inicializar ESP
local function initESP(targetPlayer)
    if espCache[targetPlayer] then return end
    if targetPlayer == player then return end

    espCache[targetPlayer] = {
        billboardGui = setupBillboardGui(targetPlayer),
        skeletonLines = createSkeletonLines(targetPlayer),
        tracer = setupTracer(targetPlayer),
    }
end

-- Limpar ESP
local function cleanupESP(targetPlayer)
    if espCache[targetPlayer] then
        if espCache[targetPlayer].billboardGui then
            espCache[targetPlayer].billboardGui:Destroy()
        end
        if espCache[targetPlayer].skeletonLines then
            espCache[targetPlayer].skeletonLines:Destroy()
        end
        if espCache[targetPlayer].tracer then
            espCache[targetPlayer].tracer:Destroy()
        end
        espCache[targetPlayer] = nil
    end
end

-- Projetar posição do mundo para a tela
local function worldToScreen(worldPos)
    local screenPos, onScreen = camera:WorldToViewportPoint(worldPos)
    if onScreen then
        return Vector2.new(screenPos.X, screenPos.Y), true
    end
    return nil, false
end

-- Desenhar linha entre dois pontos na tela
local function drawLineOnScreen(lineFrame, posA, posB, color)
    local midpoint = (posA + posB) / 2
    local length = (posB - posA).Magnitude

    if length < 1 then
        lineFrame.Visible = false
        return
    end

    local angle = math.atan2(posB.Y - posA.Y, posB.X - posA.X)

    lineFrame.Position = UDim2.new(0, midpoint.X - 1, 0, midpoint.Y - length / 2)
    lineFrame.Size = UDim2.new(0, 3, 0, length)
    lineFrame.Rotation = math.deg(angle) + 90
    lineFrame.BackgroundColor3 = color
    lineFrame.Visible = true
end

-- Pegar a posição de uma parte (Centro da CFrame)
local function getPartPos(part)
    if part then
        return part.CFrame.Position
    end
    return nil
end

-- ============================================================
--  SKELETON ESP - CORRETO COM POSIÇÕES REAIS DAS PARTES
-- ============================================================

local function updateSkeleton(targetPlayer, skeletonFolder, color)
    if not Config.esp.skeleton then
        for _, line in pairs(skeletonFolder:GetChildren()) do
            line.Visible = false
        end
        return
    end

    local character = targetPlayer.Character
    if not character then
        for _, line in pairs(skeletonFolder:GetChildren()) do
            line.Visible = false
        end
        return
    end

    local humanoid = character:FindFirstChild("Humanoid")
    if not humanoid or humanoid.Health <= 0 then
        for _, line in pairs(skeletonFolder:GetChildren()) do
            line.Visible = false
        end
        return
    end

    local rootPart = character:FindFirstChild("HumanoidRootPart")
    if not rootPart then
        for _, line in pairs(skeletonFolder:GetChildren()) do
            line.Visible = false
        end
        return
    end

    -- Detectar R15 ou R6
    local isR15 = character:FindFirstChild("UpperTorso") ~= nil

    local joints = {}

    if isR15 then
        -- ===== R15 =====
        -- Pegar todas as partes diretamente
        local head = character:FindFirstChild("Head")
        local upperTorso = character:FindFirstChild("UpperTorso")
        local lowerTorso = character:FindFirstChild("LowerTorso")
        local leftUpperArm = character:FindFirstChild("LeftUpperArm")
        local rightUpperArm = character:FindFirstChild("RightUpperArm")
        local leftLowerArm = character:FindFirstChild("LeftLowerArm")
        local rightLowerArm = character:FindFirstChild("RightLowerArm")
        local leftHand = character:FindFirstChild("LeftHand")
        local rightHand = character:FindFirstChild("RightHand")
        local leftUpperLeg = character:FindFirstChild("LeftUpperLeg")
        local rightUpperLeg = character:FindFirstChild("RightUpperLeg")
        local leftLowerLeg = character:FindFirstChild("LeftLowerLeg")
        local rightLowerLeg = character:FindFirstChild("RightLowerLeg")
        local leftFoot = character:FindFirstChild("LeftFoot")
        local rightFoot = character:FindFirstChild("RightFoot")

        -- Verificar se tem as partes necessárias
        if not upperTorso or not head then
            for _, line in pairs(skeletonFolder:GetChildren()) do
                line.Visible = false
            end
            return
        end

        -- Helper: pegar posição da borda superior de uma parte (relative ao CFrame local)
        local function topOf(part)
            return part.CFrame * CFrame.new(0, part.Size.Y / 2, 0)
        end
        local function bottomOf(part)
            return part.CFrame * CFrame.new(0, -part.Size.Y / 2, 0)
        end
        local function leftOf(part)
            return part.CFrame * CFrame.new(-part.Size.X / 2, 0, 0)
        end
        local function rightOf(part)
            return part.CFrame * CFrame.new(part.Size.X / 2, 0, 0)
        end

        -- Joints: usar POSIÇÕES REAIS das partes (CFrame.Position) que já refletem agachar/animar
        joints = {
            Head = topOf(head),
            Neck = topOf(upperTorso),
            LeftShoulder = upperTorso.CFrame * CFrame.new(-upperTorso.Size.X / 2, upperTorso.Size.Y / 3, 0),
            RightShoulder = upperTorso.CFrame * CFrame.new(upperTorso.Size.X / 2, upperTorso.Size.Y / 3, 0),
            LeftElbow = leftLowerArm and leftLowerArm.CFrame.Position or bottomOf(leftUpperArm),
            RightElbow = rightLowerArm and rightLowerArm.CFrame.Position or bottomOf(rightUpperArm),
            LeftHand = leftHand and leftHand.CFrame.Position or (leftLowerArm and bottomOf(leftLowerArm) or bottomOf(leftUpperArm)),
            RightHand = rightHand and rightHand.CFrame.Position or (rightLowerArm and bottomOf(rightLowerArm) or bottomOf(rightUpperArm)),
            LeftHip = lowerTorso.CFrame * CFrame.new(-lowerTorso.Size.X / 2, 0, 0),
            RightHip = lowerTorso.CFrame * CFrame.new(lowerTorso.Size.X / 2, 0, 0),
            LeftKnee = leftLowerLeg and leftLowerLeg.CFrame.Position or bottomOf(leftUpperLeg),
            RightKnee = rightLowerLeg and rightLowerLeg.CFrame.Position or bottomOf(rightUpperLeg),
            LeftFoot = leftFoot and leftFoot.CFrame.Position or bottomOf(leftLowerLeg),
            RightFoot = rightFoot and rightFoot.CFrame.Position or bottomOf(rightLowerLeg),
            TorsoCenter = upperTorso.CFrame.Position,
            HipCenter = lowerTorso.CFrame.Position,
        }

    else
        -- ===== R6 =====
        local torso = character:FindFirstChild("Torso")
        local head = character:FindFirstChild("Head")
        local leftArm = character:FindFirstChild("Left Arm")
        local rightArm = character:FindFirstChild("Right Arm")
        local leftLeg = character:FindFirstChild("Left Leg")
        local rightLeg = character:FindFirstChild("Right Leg")

        if not torso then
            for _, line in pairs(skeletonFolder:GetChildren()) do
                line.Visible = false
            end
            return
        end

        -- Helper functions para R6
        local function topOfR6(part)
            return part.CFrame * CFrame.new(0, part.Size.Y / 2, 0)
        end
        local function bottomOfR6(part)
            return part.CFrame * CFrame.new(0, -part.Size.Y / 2, 0)
        end

        joints = {
            Head = head and topOfR6(head) or CFrame.new(0, 0, 0),
            Neck = topOfR6(torso),
            LeftShoulder = torso.CFrame * CFrame.new(-torso.Size.X / 2, torso.Size.Y / 3, 0),
            RightShoulder = torso.CFrame * CFrame.new(torso.Size.X / 2, torso.Size.Y / 3, 0),
            LeftElbow = leftArm and leftArm.CFrame.Position or bottomOfR6(torso),
            RightElbow = rightArm and rightArm.CFrame.Position or bottomOfR6(torso),
            LeftHand = leftArm and bottomOfR6(leftArm) or CFrame.new(0, 0, 0),
            RightHand = rightArm and bottomOfR6(rightArm) or CFrame.new(0, 0, 0),
            LeftHip = torso.CFrame * CFrame.new(-torso.Size.X / 2, -torso.Size.Y / 2, 0),
            RightHip = torso.CFrame * CFrame.new(torso.Size.X / 2, -torso.Size.Y / 2, 0),
            LeftKnee = leftLeg and leftLeg.CFrame.Position or CFrame.new(0, 0, 0),
            RightKnee = rightLeg and rightLeg.CFrame.Position or CFrame.new(0, 0, 0),
            LeftFoot = leftLeg and bottomOfR6(leftLeg) or CFrame.new(0, 0, 0),
            RightFoot = rightLeg and bottomOfR6(rightLeg) or CFrame.new(0, 0, 0),
            TorsoCenter = torso.CFrame.Position,
            HipCenter = bottomOfR6(torso),
        }
    end

    -- Desenhar as linhas do esqueleto
    local lineNeck = skeletonFolder:FindFirstChild("Line_Neck")
    local lineLShoulder = skeletonFolder:FindFirstChild("Line_LShoulder")
    local lineRShoulder = skeletonFolder:FindFirstChild("Line_RShoulder")
    local lineLElbow = skeletonFolder:FindFirstChild("Line_LElbow")
    local lineRElbow = skeletonFolder:FindFirstChild("Line_RElbow")
    local lineLHand = skeletonFolder:FindFirstChild("Line_LHand")
    local lineRHand = skeletonFolder:FindFirstChild("Line_RHand")
    local lineSpine = skeletonFolder:FindFirstChild("Line_Spine")
    local lineLHip = skeletonFolder:FindFirstChild("Line_LHip")
    local lineRHip = skeletonFolder:FindFirstChild("Line_RHip")
    local lineLKnee = skeletonFolder:FindFirstChild("Line_LKnee")
    local lineRKnee = skeletonFolder:FindFirstChild("Line_RKnee")
    local lineLFoot = skeletonFolder:FindFirstChild("Line_LFoot")
    local lineRFoot = skeletonFolder:FindFirstChild("Line_RFoot")

    -- Helper: converter CFrame ou Vector3 para Vector3
    local function toPos(val)
        if typeof(val) == "Vector3" then return val end
        return val.Position
    end

    -- Cabeça -> Pescoço
    if lineNeck and joints.Head and joints.Neck then
        local sA, oA = worldToScreen(toPos(joints.Head))
        local sB, oB = worldToScreen(toPos(joints.Neck))
        if oA and oB then drawLineOnScreen(lineNeck, sA, sB, color) else lineNeck.Visible = false end
    end

    -- Pescoço -> Ombro Esquerdo
    if lineLShoulder and joints.Neck and joints.LeftShoulder then
        local sA, oA = worldToScreen(toPos(joints.Neck))
        local sB, oB = worldToScreen(toPos(joints.LeftShoulder))
        if oA and oB then drawLineOnScreen(lineLShoulder, sA, sB, color) else lineLShoulder.Visible = false end
    end

    -- Pescoço -> Ombro Direito
    if lineRShoulder and joints.Neck and joints.RightShoulder then
        local sA, oA = worldToScreen(toPos(joints.Neck))
        local sB, oB = worldToScreen(toPos(joints.RightShoulder))
        if oA and oB then drawLineOnScreen(lineRShoulder, sA, sB, color) else lineRShoulder.Visible = false end
    end

    -- Ombro Esquerdo -> Cotovelo Esquerdo
    if lineLElbow and joints.LeftShoulder and joints.LeftElbow then
        local sA, oA = worldToScreen(toPos(joints.LeftShoulder))
        local sB, oB = worldToScreen(toPos(joints.LeftElbow))
        if oA and oB then drawLineOnScreen(lineLElbow, sA, sB, color) else lineLElbow.Visible = false end
    end

    -- Ombro Direito -> Cotovelo Direito
    if lineRElbow and joints.RightShoulder and joints.RightElbow then
        local sA, oA = worldToScreen(toPos(joints.RightShoulder))
        local sB, oB = worldToScreen(toPos(joints.RightElbow))
        if oA and oB then drawLineOnScreen(lineRElbow, sA, sB, color) else lineRElbow.Visible = false end
    end

    -- Cotovelo Esquerdo -> Mão Esquerda
    if lineLHand and joints.LeftElbow and joints.LeftHand then
        local sA, oA = worldToScreen(toPos(joints.LeftElbow))
        local sB, oB = worldToScreen(toPos(joints.LeftHand))
        if oA and oB then drawLineOnScreen(lineLHand, sA, sB, color) else lineLHand.Visible = false end
    end

    -- Cotovelo Direito -> Mão Direita
    if lineRHand and joints.RightElbow and joints.RightHand then
        local sA, oA = worldToScreen(toPos(joints.RightElbow))
        local sB, oB = worldToScreen(toPos(joints.RightHand))
        if oA and oB then drawLineOnScreen(lineRHand, sA, sB, color) else lineRHand.Visible = false end
    end

    -- Torso Superior -> Torso Inferior (Coluna)
    if lineSpine and joints.TorsoCenter and joints.HipCenter then
        local sA, oA = worldToScreen(toPos(joints.TorsoCenter))
        local sB, oB = worldToScreen(toPos(joints.HipCenter))
        if oA and oB then drawLineOnScreen(lineSpine, sA, sB, color) else lineSpine.Visible = false end
    end

    -- Quadril Esquerdo -> Quadril Inferior
    if lineLHip and joints.HipCenter and joints.LeftHip then
        local sA, oA = worldToScreen(toPos(joints.HipCenter))
        local sB, oB = worldToScreen(toPos(joints.LeftHip))
        if oA and oB then drawLineOnScreen(lineLHip, sA, sB, color) else lineLHip.Visible = false end
    end

    -- Quadril Direito -> Quadril Inferior
    if lineRHip and joints.HipCenter and joints.RightHip then
        local sA, oA = worldToScreen(toPos(joints.HipCenter))
        local sB, oB = worldToScreen(toPos(joints.RightHip))
        if oA and oB then drawLineOnScreen(lineRHip, sA, sB, color) else lineRHip.Visible = false end
    end

    -- Joelho Esquerdo -> Quadril Esquerdo
    if lineLKnee and joints.LeftHip and joints.LeftKnee then
        local sA, oA = worldToScreen(toPos(joints.LeftHip))
        local sB, oB = worldToScreen(toPos(joints.LeftKnee))
        if oA and oB then drawLineOnScreen(lineLKnee, sA, sB, color) else lineLKnee.Visible = false end
    end

    -- Joelho Direito -> Quadril Direito
    if lineRKnee and joints.RightHip and joints.RightKnee then
        local sA, oA = worldToScreen(toPos(joints.RightHip))
        local sB, oB = worldToScreen(toPos(joints.RightKnee))
        if oA and oB then drawLineOnScreen(lineRKnee, sA, sB, color) else lineRKnee.Visible = false end
    end

    -- Pé Esquerdo -> Joelho Esquerdo
    if lineLFoot and joints.LeftKnee and joints.LeftFoot then
        local sA, oA = worldToScreen(toPos(joints.LeftKnee))
        local sB, oB = worldToScreen(toPos(joints.LeftFoot))
        if oA and oB then drawLineOnScreen(lineLFoot, sA, sB, color) else lineLFoot.Visible = false end
    end

    -- Pé Direito -> Joelho Direito
    if lineRFoot and joints.RightKnee and joints.RightFoot then
        local sA, oA = worldToScreen(toPos(joints.RightKnee))
        local sB, oB = worldToScreen(toPos(joints.RightFoot))
        if oA and oB then drawLineOnScreen(lineRFoot, sA, sB, color) else lineRFoot.Visible = false end
    end
end

-- Atualizar ESP completo
local function updateESP(targetPlayer, espData)
    local character = targetPlayer.Character
    local rootPart = character and character:FindFirstChild("HumanoidRootPart")
    local humanoid = character and character:FindFirstChild("Humanoid")

    if not rootPart or not humanoid or humanoid.Health <= 0 then
        if espData.billboardGui then espData.billboardGui.Enabled = false end
        if espData.skeletonLines then
            for _, line in pairs(espData.skeletonLines:GetChildren()) do
                line.Visible = false
            end
        end
        if espData.tracer then espData.tracer.Visible = false end
        return
    end

    -- Team check
    if Config.teamCheck.enabled and player.Team and targetPlayer.Team then
        if player.Team == targetPlayer.Team and not Config.esp.showTeam then
            if espData.billboardGui then espData.billboardGui.Enabled = false end
            if espData.skeletonLines then
                for _, line in pairs(espData.skeletonLines:GetChildren()) do
                    line.Visible = false
                end
            end
            if espData.tracer then espData.tracer.Visible = false end
            return
        end
    end

    if not Config.esp.enabled then
        if espData.billboardGui then espData.billboardGui.Enabled = false end
        if espData.skeletonLines then
            for _, line in pairs(espData.skeletonLines:GetChildren()) do
                line.Visible = false
            end
        end
        if espData.tracer then espData.tracer.Visible = false end
        return
    end

    local color = getPlayerColor(targetPlayer)

    -- Billboard Gui
    local bgui = espData.billboardGui
    if bgui then
        bgui.Adornee = rootPart
        bgui.Enabled = true

        local nameLabel = bgui:FindFirstChild("ESP_Name")
        if nameLabel then
            nameLabel.Text = targetPlayer.Name
            nameLabel.TextColor3 = color
            nameLabel.Visible = Config.esp.names
        end

        local hpBg = bgui:FindFirstChild("ESP_HPBg")
        local hpFill = bgui:FindFirstChild("ESP_HPFill")
        if hpBg and hpFill then
            hpBg.Visible = Config.esp.health
            if Config.esp.health then
                local hpRatio = math.clamp(humanoid.Health / humanoid.MaxHealth, 0, 1)
                hpFill.Size = UDim2.new(hpRatio, 0, 1, 0)
                if hpRatio > 0.6 then
                    hpFill.BackgroundColor3 = Color3.fromRGB(50, 220, 100)
                elseif hpRatio > 0.3 then
                    hpFill.BackgroundColor3 = Color3.fromRGB(255, 200, 50)
                else
                    hpFill.BackgroundColor3 = Color3.fromRGB(255, 50, 50)
                end
            end
        end

        local distLabel = bgui:FindFirstChild("ESP_Distance")
        if distLabel then
            distLabel.Visible = Config.esp.distance
            if Config.esp.distance then
                local char = player.Character
                local distance = char and char:FindFirstChild("HumanoidRootPart") and
                    math.floor((char.HumanoidRootPart.Position - rootPart.Position).Magnitude) or 0
                distLabel.Text = tostring(distance) .. "m"
            end
        end
    end

    -- Skeleton
    if espData.skeletonLines then
        updateSkeleton(targetPlayer, espData.skeletonLines, color)
    end

    -- Tracer
    local tracer = espData.tracer
    if tracer then
        tracer.Visible = Config.esp.tracers
        if Config.esp.tracers then
            local screenPos, onScreen = camera:WorldToViewportPoint(rootPart.Position)
            if onScreen then
                local viewportSize = camera.ViewportSize
                local startX = viewportSize.X / 2
                local startY = viewportSize.Y
                local length = (Vector2.new(screenPos.X, screenPos.Y) - Vector2.new(startX, startY)).Magnitude
                local angle = math.atan2(screenPos.Y - startY, screenPos.X - startX)

                tracer.Position = UDim2.new(0, startX, 0, startY)
                tracer.Size = UDim2.new(0, 2, 0, length)
                tracer.Rotation = math.deg(angle) + 90
                tracer.BackgroundColor3 = color
            else
                tracer.Visible = false
            end
        end
    end
end

-- Loop principal do ESP
RunService.RenderStepped:Connect(function()
    for _, targetPlayer in pairs(Players:GetPlayers()) do
        if targetPlayer ~= player then
            if not espCache[targetPlayer] then
                initESP(targetPlayer)
            end
            if espCache[targetPlayer] then
                updateESP(targetPlayer, espCache[targetPlayer])
            end
        end
    end
end)

-- Função que monitora character de um jogador e cria/recria ESP
local function monitorCharacter(targetPlayer)
    if targetPlayer == player then return end
    
    targetPlayer.CharacterAdded:Connect(function(char)
        -- Destruir ESP anterior se existir
        if espCache[targetPlayer] then
            if espCache[targetPlayer].billboardGui then espCache[targetPlayer].billboardGui:Destroy() end
            if espCache[targetPlayer].skeletonLines then espCache[targetPlayer].skeletonLines:Destroy() end
            if espCache[targetPlayer].tracer then espCache[targetPlayer].tracer:Destroy() end
            espCache[targetPlayer] = nil
        end
        -- Aguardar o character carregar
        task.wait(0.3)
        if char:FindFirstChild("HumanoidRootPart") then
            initESP(targetPlayer)
            print("[ProMenu] ESP criado para: " .. targetPlayer.Name)
        end
    end)
    
    -- Se já tem character carregado, iniciar ESP direto
    if targetPlayer.Character and targetPlayer.Character:FindFirstChild("HumanoidRootPart") then
        task.wait(0.3)
        if not espCache[targetPlayer] then
            initESP(targetPlayer)
            print("[ProMenu] ESP criado para: " .. targetPlayer.Name)
        end
    end
end

-- Monitorar jogador adicionado
Players.PlayerAdded:Connect(function(newPlayer)
    monitorCharacter(newPlayer)
end)

-- Inicializar para jogadores já no jogo
for _, existingPlayer in pairs(Players:GetPlayers()) do
    if existingPlayer ~= player then
        monitorCharacter(existingPlayer)
    end
end

-- Refresh do ESP a cada 10 segundos (detectar novos jogadores mais rápido)
task.spawn(function()
    while true do
        task.wait(10)
        for _, targetPlayer in pairs(Players:GetPlayers()) do
            if targetPlayer ~= player and not espCache[targetPlayer] then
                monitorCharacter(targetPlayer)
            end
        end
        print("[ProMenu] ESP refresh — Total jogadores: " .. #Players:GetPlayers() - 1)
    end
end)

-- ============================================================
--  TEAM CHECK
-- ============================================================

local function isEnemy(targetPlayer)
    if not Config.teamCheck.enabled then return true end
    if not player.Team or not targetPlayer.Team then return true end
    return player.Team ~= targetPlayer.Team
end

-- ============================================================
--  ANTI-ESCAPE + TOGGLE MOUSE (ALT)
-- ============================================================

-- Estado do mouse: bloqueado por padrão
local mouseLocked = true

local function hideAllCoreGui()
    pcall(function()
        StarterGui:SetCoreGuiEnabled(Enum.CoreGuiType.All, false)
    end)
    pcall(function()
        StarterGui:SetCoreGuiEnabled(Enum.CoreGuiType.Backpack, false)
        StarterGui:SetCoreGuiEnabled(Enum.CoreGuiType.PlayerList, false)
        StarterGui:SetCoreGuiEnabled(Enum.CoreGuiType.Chat, false)
        StarterGui:SetCoreGuiEnabled(Enum.CoreGuiType.EmotesMenu, false)
        StarterGui:SetCoreGuiEnabled(Enum.CoreGuiType.Health, false)
        StarterGui:SetCoreGuiEnabled(Enum.CoreGuiType.ResetButton, false)
    end)
end

local function blockMouse()
    mouseLocked = true
    -- Bloquear mouse
    pcall(function()
        game:GetService("UserInputService").MouseBehavior = Enum.MouseBehavior.LockCurrentPosition
    end)
    -- Esconder CoreGui
    hideAllCoreGui()
    print("[ProMenu] Mouse bloqueado — ESC + CoreGui desativados")
end

local function freeMouse()
    mouseLocked = false
    -- Liberar mouse
    pcall(function()
        game:GetService("UserInputService").MouseBehavior = Enum.MouseBehavior.Default
    end)
    -- Mostrar CoreGui (se quiser)
    pcall(function()
        StarterGui:SetCoreGuiEnabled(Enum.CoreGuiType.All, true)
    end)
    print("[ProMenu] Mouse liberado — Mexa na GUI!")
end

-- Tecla ALT: toggle mouse livre/bloqueado
UserInputService.InputBegan:Connect(function(input, gameProcessed)
    if input.KeyCode == Enum.KeyCode.LeftAlt or input.KeyCode == Enum.KeyCode.RightAlt then
        if mouseLocked then
            freeMouse()
        else
            blockMouse()
        end
    end
end)

-- ESC: esconder CoreGui se tentar abrir (mas só se mouse estiver bloqueado)
UserInputService.InputBegan:Connect(function(input, gameProcessed)
    if input.KeyCode == Enum.KeyCode.Escape then
        if mouseLocked then
            task.defer(hideAllCoreGui)
        end
    end
end)

-- CoreGuiChangedSignal: fechar imediatamente se reabrir
StarterGui.CoreGuiChangedSignal:Connect(function(coreGuiType, enabled)
    if enabled and mouseLocked then
        task.defer(hideAllCoreGui)
    end
end)

-- Loop de reaplicação (só se mouse bloqueado)
task.spawn(function()
    while true do
        task.wait(0.5)
        if mouseLocked then
            hideAllCoreGui()
            pcall(function()
                local coreGui = game.CoreGui:FindFirstChild("RobloxGui")
                if coreGui and coreGui:FindFirstChild("MainMenu") then
                    coreGui:FindFirstChild("MainMenu").Enabled = false
                end
            end)
        end
    end
end)

-- Inicializar bloqueado
blockMouse()

print("[ProMenu] Anti-Escape ativo — Pressione ALT para liberar o mouse")

-- ============================================================
--  NOTIFICAÇÃO
-- ============================================================

print("[ProMenu] Menu carregado com sucesso!")
print("[ProMenu] Features: Aimbot, Skeleton ESP, Team Check")
print("[ProMenu] Arraste pela barra de título para mover")

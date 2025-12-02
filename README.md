-- Brookhaven Script Hub Completo V2
-- Design Moderno + Totalmente Funcional

local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local UserInputService = game:GetService("UserInputService")
local TweenService = game:GetService("TweenService")
local CoreGui = game:GetService("CoreGui")
local LocalPlayer = Players.LocalPlayer
local Camera = workspace.CurrentCamera

-- Variáveis de Estado
local flyEnabled = false
local noclipEnabled = false
local espEnabled = false
local flySpeed = 50
local flyConnection
local noclipConnection

-- Limpar GUIs antigas
pcall(function()
    if CoreGui:FindFirstChild("BrookhavenHub") then
        CoreGui.BrookhavenHub:Destroy()
    end
end)

-- Criar GUI Principal
local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Name = "BrookhavenHub"
ScreenGui.Parent = CoreGui
ScreenGui.ZIndexBehavior = Enum.ZIndexBehavior.Sibling
ScreenGui.ResetOnSpawn = false

-- Frame Principal com transparência
local MainFrame = Instance.new("Frame")
MainFrame.Name = "MainFrame"
MainFrame.Parent = ScreenGui
MainFrame.BackgroundColor3 = Color3.fromRGB(15, 15, 15)
MainFrame.BackgroundTransparency = 0.15
MainFrame.BorderSizePixel = 0
MainFrame.Position = UDim2.new(0.35, 0, 0.15, 0)
MainFrame.Size = UDim2.new(0, 450, 0, 550)
MainFrame.Active = true
MainFrame.Draggable = true

local MainCorner = Instance.new("UICorner")
MainCorner.CornerRadius = UDim.new(0, 15)
MainCorner.Parent = MainFrame

-- Sombra/Brilho
local MainStroke = Instance.new("UIStroke")
MainStroke.Color = Color3.fromRGB(100, 200, 255)
MainStroke.Thickness = 2
MainStroke.Transparency = 0.5
MainStroke.Parent = MainFrame

-- Efeito de Gradiente
local Gradient = Instance.new("UIGradient")
Gradient.Color = ColorSequence.new{
    ColorSequenceKeypoint.new(0, Color3.fromRGB(20, 20, 30)),
    ColorSequenceKeypoint.new(1, Color3.fromRGB(10, 10, 20))
}
Gradient.Rotation = 45
Gradient.Parent = MainFrame

-- Barra de Título Moderna
local TitleBar = Instance.new("Frame")
TitleBar.Name = "TitleBar"
TitleBar.Parent = MainFrame
TitleBar.BackgroundColor3 = Color3.fromRGB(10, 10, 15)
TitleBar.BackgroundTransparency = 0.3
TitleBar.BorderSizePixel = 0
TitleBar.Size = UDim2.new(1, 0, 0, 50)

local TitleCorner = Instance.new("UICorner")
TitleCorner.CornerRadius = UDim.new(0, 15)
TitleCorner.Parent = TitleBar

local TitleLabel = Instance.new("TextLabel")
TitleLabel.Parent = TitleBar
TitleLabel.BackgroundTransparency = 1
TitleLabel.Position = UDim2.new(0, 15, 0, 0)
TitleLabel.Size = UDim2.new(0.6, 0, 1, 0)
TitleLabel.Font = Enum.Font.GothamBold
TitleLabel.Text = "🏠 BROOKHAVEN HUB"
TitleLabel.TextColor3 = Color3.fromRGB(100, 200, 255)
TitleLabel.TextSize = 20
TitleLabel.TextXAlignment = Enum.TextXAlignment.Left

local VersionLabel = Instance.new("TextLabel")
VersionLabel.Parent = TitleBar
VersionLabel.BackgroundTransparency = 1
VersionLabel.Position = UDim2.new(0, 15, 0, 25)
VersionLabel.Size = UDim2.new(0.6, 0, 0.5, 0)
VersionLabel.Font = Enum.Font.Gotham
VersionLabel.Text = "v2.0 Premium"
VersionLabel.TextColor3 = Color3.fromRGB(150, 150, 150)
VersionLabel.TextSize = 12
VersionLabel.TextXAlignment = Enum.TextXAlignment.Left

-- Botão Fechar
local CloseButton = Instance.new("TextButton")
CloseButton.Parent = TitleBar
CloseButton.BackgroundColor3 = Color3.fromRGB(255, 70, 70)
CloseButton.BackgroundTransparency = 0.2
CloseButton.BorderSizePixel = 0
CloseButton.Position = UDim2.new(1, -40, 0.5, -15)
CloseButton.Size = UDim2.new(0, 30, 0, 30)
CloseButton.Font = Enum.Font.GothamBold
CloseButton.Text = "✕"
CloseButton.TextColor3 = Color3.fromRGB(255, 255, 255)
CloseButton.TextSize = 16

local CloseCorner = Instance.new("UICorner")
CloseCorner.CornerRadius = UDim.new(1, 0)
CloseCorner.Parent = CloseButton

-- Botão Minimizar
local MinimizeButton = Instance.new("TextButton")
MinimizeButton.Parent = TitleBar
MinimizeButton.BackgroundColor3 = Color3.fromRGB(255, 200, 70)
MinimizeButton.BackgroundTransparency = 0.2
MinimizeButton.BorderSizePixel = 0
MinimizeButton.Position = UDim2.new(1, -75, 0.5, -15)
MinimizeButton.Size = UDim2.new(0, 30, 0, 30)
MinimizeButton.Font = Enum.Font.GothamBold
MinimizeButton.Text = "—"
MinimizeButton.TextColor3 = Color3.fromRGB(255, 255, 255)
MinimizeButton.TextSize = 16

local MinCorner = Instance.new("UICorner")
MinCorner.CornerRadius = UDim.new(1, 0)
MinCorner.Parent = MinimizeButton

-- ScrollFrame
local ScrollFrame = Instance.new("ScrollingFrame")
ScrollFrame.Parent = MainFrame
ScrollFrame.BackgroundTransparency = 1
ScrollFrame.BorderSizePixel = 0
ScrollFrame.Position = UDim2.new(0, 15, 0, 65)
ScrollFrame.Size = UDim2.new(1, -30, 1, -80)
ScrollFrame.CanvasSize = UDim2.new(0, 0, 0, 0)
ScrollFrame.ScrollBarThickness = 4
ScrollFrame.ScrollBarImageColor3 = Color3.fromRGB(100, 200, 255)

local UIListLayout = Instance.new("UIListLayout")
UIListLayout.Parent = ScrollFrame
UIListLayout.SortOrder = Enum.SortOrder.LayoutOrder
UIListLayout.Padding = UDim.new(0, 10)

-- Função para criar botões modernos
local function createButton(text, emoji, callback, isToggle)
    local Button = Instance.new("TextButton")
    Button.Parent = ScrollFrame
    Button.BackgroundColor3 = Color3.fromRGB(30, 30, 40)
    Button.BackgroundTransparency = 0.3
    Button.BorderSizePixel = 0
    Button.Size = UDim2.new(1, 0, 0, 45)
    Button.Font = Enum.Font.GothamSemibold
    Button.Text = ""
    Button.TextColor3 = Color3.fromRGB(255, 255, 255)
    Button.TextSize = 15
    Button.AutoButtonColor = false
    
    local ButtonCorner = Instance.new("UICorner")
    ButtonCorner.CornerRadius = UDim.new(0, 10)
    ButtonCorner.Parent = Button
    
    local ButtonStroke = Instance.new("UIStroke")
    ButtonStroke.Color = Color3.fromRGB(100, 200, 255)
    ButtonStroke.Thickness = 1
    ButtonStroke.Transparency = 0.7
    ButtonStroke.Parent = Button
    
    local EmojiLabel = Instance.new("TextLabel")
    EmojiLabel.Parent = Button
    EmojiLabel.BackgroundTransparency = 1
    EmojiLabel.Position = UDim2.new(0, 15, 0, 0)
    EmojiLabel.Size = UDim2.new(0, 30, 1, 0)
    EmojiLabel.Font = Enum.Font.GothamBold
    EmojiLabel.Text = emoji
    EmojiLabel.TextColor3 = Color3.fromRGB(100, 200, 255)
    EmojiLabel.TextSize = 20
    
    local TextLabel = Instance.new("TextLabel")
    TextLabel.Parent = Button
    TextLabel.BackgroundTransparency = 1
    TextLabel.Position = UDim2.new(0, 50, 0, 0)
    TextLabel.Size = UDim2.new(1, -100, 1, 0)
    TextLabel.Font = Enum.Font.GothamSemibold
    TextLabel.Text = text
    TextLabel.TextColor3 = Color3.fromRGB(255, 255, 255)
    TextLabel.TextSize = 14
    TextLabel.TextXAlignment = Enum.TextXAlignment.Left
    
    local StatusLabel = Instance.new("TextLabel")
    StatusLabel.Parent = Button
    StatusLabel.BackgroundTransparency = 1
    StatusLabel.Position = UDim2.new(1, -60, 0, 0)
    StatusLabel.Size = UDim2.new(0, 50, 1, 0)
    StatusLabel.Font = Enum.Font.GothamBold
    StatusLabel.Text = isToggle and "OFF" or ""
    StatusLabel.TextColor3 = Color3.fromRGB(255, 100, 100)
    StatusLabel.TextSize = 12
    
    if isToggle then
        local isEnabled = false
        Button.MouseButton1Click:Connect(function()
            isEnabled = not isEnabled
            StatusLabel.Text = isEnabled and "ON" or "OFF"
            StatusLabel.TextColor3 = isEnabled and Color3.fromRGB(100, 255, 100) or Color3.fromRGB(255, 100, 100)
            Button.BackgroundColor3 = isEnabled and Color3.fromRGB(40, 60, 40) or Color3.fromRGB(30, 30, 40)
            ButtonStroke.Color = isEnabled and Color3.fromRGB(100, 255, 100) or Color3.fromRGB(100, 200, 255)
            callback(isEnabled)
        end)
    else
        Button.MouseButton1Click:Connect(callback)
    end
    
    -- Efeito Hover
    Button.MouseEnter:Connect(function()
        TweenService:Create(Button, TweenInfo.new(0.2), {BackgroundTransparency = 0.1}):Play()
        TweenService:Create(ButtonStroke, TweenInfo.new(0.2), {Transparency = 0.3}):Play()
    end)
    
    Button.MouseLeave:Connect(function()
        TweenService:Create(Button, TweenInfo.new(0.2), {BackgroundTransparency = 0.3}):Play()
        TweenService:Create(ButtonStroke, TweenInfo.new(0.2), {Transparency = 0.7}):Play()
    end)
    
    return Button
end

-- Sistema FLY Melhorado
local function toggleFly(enabled)
    flyEnabled = enabled
    
    if flyConnection then
        flyConnection:Disconnect()
        flyConnection = nil
    end
    
    if enabled then
        local character = LocalPlayer.Character
        if not character then return end
        
        local humanoid = character:FindFirstChildOfClass("Humanoid")
        local rootPart = character:FindFirstChild("HumanoidRootPart")
        if not rootPart or not humanoid then return end
        
        local bodyGyro = Instance.new("BodyGyro")
        bodyGyro.MaxTorque = Vector3.new(9e9, 9e9, 9e9)
        bodyGyro.P = 9e4
        bodyGyro.Parent = rootPart
        
        local bodyVelocity = Instance.new("BodyVelocity")
        bodyVelocity.MaxForce = Vector3.new(9e9, 9e9, 9e9)
        bodyVelocity.Velocity = Vector3.new(0, 0, 0)
        bodyVelocity.Parent = rootPart
        
        flyConnection = RunService.Heartbeat:Connect(function()
            if not flyEnabled or not rootPart or not rootPart.Parent then
                if bodyGyro then bodyGyro:Destroy() end
                if bodyVelocity then bodyVelocity:Destroy() end
                return
            end
            
            local moveDirection = Vector3.new(0, 0, 0)
            local speed = flySpeed
            
            if UserInputService:IsKeyDown(Enum.KeyCode.W) then
                moveDirection = moveDirection + (Camera.CFrame.LookVector)
            end
            if UserInputService:IsKeyDown(Enum.KeyCode.S) then
                moveDirection = moveDirection - (Camera.CFrame.LookVector)
            end
            if UserInputService:IsKeyDown(Enum.KeyCode.A) then
                moveDirection = moveDirection - (Camera.CFrame.RightVector)
            end
            if UserInputService:IsKeyDown(Enum.KeyCode.D) then
                moveDirection = moveDirection + (Camera.CFrame.RightVector)
            end
            if UserInputService:IsKeyDown(Enum.KeyCode.Space) then
                moveDirection = moveDirection + Vector3.new(0, 1, 0)
            end
            if UserInputService:IsKeyDown(Enum.KeyCode.LeftShift) then
                moveDirection = moveDirection - Vector3.new(0, 1, 0)
            end
            if UserInputService:IsKeyDown(Enum.KeyCode.LeftControl) then
                speed = flySpeed * 2
            end
            
            bodyVelocity.Velocity = moveDirection.Unit * speed
            bodyGyro.CFrame = Camera.CFrame
        end)
    else
        local character = LocalPlayer.Character
        if character then
            local rootPart = character:FindFirstChild("HumanoidRootPart")
            if rootPart then
                for _, v in pairs(rootPart:GetChildren()) do
                    if v:IsA("BodyGyro") or v:IsA("BodyVelocity") then
                        v:Destroy()
                    end
                end
            end
        end
    end
end

-- Sistema NOCLIP Melhorado
local function toggleNoclip(enabled)
    noclipEnabled = enabled
    
    if noclipConnection then
        noclipConnection:Disconnect()
        noclipConnection = nil
    end
    
    if enabled then
        noclipConnection = RunService.Stepped:Connect(function()
            if noclipEnabled then
                local character = LocalPlayer.Character
                if character then
                    for _, part in pairs(character:GetDescendants()) do
                        if part:IsA("BasePart") and part.CanCollide then
                            part.CanCollide = false
                        end
                    end
                end
            end
        end)
    else
        local character = LocalPlayer.Character
        if character then
            for _, part in pairs(character:GetDescendants()) do
                if part:IsA("BasePart") then
                    part.CanCollide = true
                end
            end
        end
    end
end

-- Sistema ESP Melhorado
local espConnections = {}

local function removeESP()
    for _, connection in pairs(espConnections) do
        if connection then
            connection:Disconnect()
        end
    end
    espConnections = {}
    
    for _, player in pairs(Players:GetPlayers()) do
        if player.Character then
            for _, v in pairs(player.Character:GetDescendants()) do
                if v:IsA("BillboardGui") and v.Name == "ESP" then
                    v:Destroy()
                end
                if v:IsA("Highlight") then
                    v:Destroy()
                end
            end
        end
    end
end

local function createESP(player)
    if player == LocalPlayer then return end
    
    local function addHighlight(character)
        local highlight = Instance.new("Highlight")
        highlight.Name = "ESP"
        highlight.Parent = character
        highlight.FillColor = Color3.fromRGB(255, 100, 100)
        highlight.OutlineColor = Color3.fromRGB(255, 255, 255)
        highlight.FillTransparency = 0.5
        highlight.OutlineTransparency = 0
        
        local humanoidRootPart = character:WaitForChild("HumanoidRootPart", 5)
        if humanoidRootPart then
            local billboard = Instance.new("BillboardGui")
            billboard.Name = "ESP"
            billboard.Parent = humanoidRootPart
            billboard.AlwaysOnTop = true
            billboard.Size = UDim2.new(0, 100, 0, 50)
            billboard.StudsOffset = Vector3.new(0, 4, 0)
            
            local nameLabel = Instance.new("TextLabel")
            nameLabel.Parent = billboard
            nameLabel.BackgroundColor3 = Color3.fromRGB(0, 0, 0)
            nameLabel.BackgroundTransparency = 0.5
            nameLabel.Size = UDim2.new(1, 0, 1, 0)
            nameLabel.Font = Enum.Font.GothamBold
            nameLabel.TextColor3 = Color3.fromRGB(255, 255, 255)
            nameLabel.TextSize = 14
            nameLabel.TextStrokeTransparency = 0
            
            local connection = RunService.RenderStepped:Connect(function()
                if player.Character and LocalPlayer.Character then
                    local dist = (player.Character.HumanoidRootPart.Position - LocalPlayer.Character.HumanoidRootPart.Position).Magnitude
                    nameLabel.Text = player.Name .. "\n[" .. math.floor(dist) .. "m]"
                end
            end)
            
            table.insert(espConnections, connection)
        end
    end
    
    if player.Character then
        addHighlight(player.Character)
    end
    
    local connection = player.CharacterAdded:Connect(function(character)
        if espEnabled then
            addHighlight(character)
        end
    end)
    
    table.insert(espConnections, connection)
end

local function toggleESP(enabled)
    espEnabled = enabled
    
    if enabled then
        for _, player in pairs(Players:GetPlayers()) do
            createESP(player)
        end
        
        local connection = Players.PlayerAdded:Connect(function(player)
            if espEnabled then
                createESP(player)
            end
        end)
        table.insert(espConnections, connection)
    else
        removeESP()
    end
end

-- Notificação moderna
local function notify(title, text, duration)
    game.StarterGui:SetCore("SendNotification", {
        Title = title;
        Text = text;
        Duration = duration or 3;
        Icon = "rbxassetid://6031075938";
    })
end

-- Criar lista de jogadores
local function showPlayerList()
    local PlayerListGui = Instance.new("ScreenGui")
    PlayerListGui.Parent = CoreGui
    PlayerListGui.ZIndexBehavior = Enum.ZIndexBehavior.Sibling
    
    local ListFrame = Instance.new("Frame")
    ListFrame.Parent = PlayerListGui
    ListFrame.BackgroundColor3 = Color3.fromRGB(15, 15, 15)
    ListFrame.BackgroundTransparency = 0.15
    ListFrame.Position = UDim2.new(0.5, -200, 0.5, -250)
    ListFrame.Size = UDim2.new(0, 400, 0, 500)
    ListFrame.BorderSizePixel = 0
    
    local Corner = Instance.new("UICorner")
    Corner.CornerRadius = UDim.new(0, 15)
    Corner.Parent = ListFrame
    
    local Stroke = Instance.new("UIStroke")
    Stroke.Color = Color3.fromRGB(100, 200, 255)
    Stroke.Thickness = 2
    Stroke.Transparency = 0.5
    Stroke.Parent = ListFrame
    
    local Title = Instance.new("TextLabel")
    Title.Parent = ListFrame
    Title.BackgroundTransparency = 1
    Title.Size = UDim2.new(1, 0, 0, 50)
    Title.Font = Enum.Font.GothamBold
    Title.Text = "👥 JOGADORES ONLINE (" .. #Players:GetPlayers() .. ")"
    Title.TextColor3 = Color3.fromRGB(100, 200, 255)
    Title.TextSize = 18
    
    local ScrollList = Instance.new("ScrollingFrame")
    ScrollList.Parent = ListFrame
    ScrollList.BackgroundTransparency = 1
    ScrollList.Position = UDim2.new(0, 10, 0, 60)
    ScrollList.Size = UDim2.new(1, -20, 1, -120)
    ScrollList.CanvasSize = UDim2.new(0, 0, 0, 0)
    ScrollList.ScrollBarThickness = 4
    ScrollList.ScrollBarImageColor3 = Color3.fromRGB(100, 200, 255)
    
    local ListLayout = Instance.new("UIListLayout")
    ListLayout.Parent = ScrollList
    ListLayout.Padding = UDim.new(0, 5)
    
    for _, player in pairs(Players:GetPlayers()) do
        local PlayerButton = Instance.new("TextButton")
        PlayerButton.Parent = ScrollList
        PlayerButton.BackgroundColor3 = Color3.fromRGB(30, 30, 40)
        PlayerButton.BackgroundTransparency = 0.3
        PlayerButton.Size = UDim2.new(1, 0, 0, 40)
        PlayerButton.Font = Enum.Font.Gotham
        PlayerButton.Text = "  " .. player.Name
        PlayerButton.TextColor3 = Color3.fromRGB(255, 255, 255)
        PlayerButton.TextSize = 14
        PlayerButton.TextXAlignment = Enum.TextXAlignment.Left
        
        local BtnCorner = Instance.new("UICorner")
        BtnCorner.CornerRadius = UDim.new(0, 8)
        BtnCorner.Parent = PlayerButton
        
        PlayerButton.MouseButton1Click:Connect(function()
            if player ~= LocalPlayer and LocalPlayer.Character and player.Character then
                local hrp = LocalPlayer.Character:FindFirstChild("HumanoidRootPart")
                local targetHrp = player.Character:FindFirstChild("HumanoidRootPart")
                if hrp and targetHrp then
                    hrp.CFrame = targetHrp.CFrame + Vector3.new(0, 2, 0)
                    notify("Teleporte", "Teleportado para " .. player.Name, 2)
                end
            end
        end)
    end
    
    ScrollList.CanvasSize = UDim2.new(0, 0, 0, ListLayout.AbsoluteContentSize.Y + 10)
    
    local CloseBtn = Instance.new("TextButton")
    CloseBtn.Parent = ListFrame
    CloseBtn.BackgroundColor3 = Color3.fromRGB(255, 70, 70)
    CloseBtn.BackgroundTransparency = 0.2
    CloseBtn.Position = UDim2.new(0.5, -60, 1, -50)
    CloseBtn.Size = UDim2.new(0, 120, 0, 35)
    CloseBtn.Font = Enum.Font.GothamBold
    CloseBtn.Text = "✕ FECHAR"
    CloseBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
    CloseBtn.TextSize = 14
    
    local CloseCorner = Instance.new("UICorner")
    CloseCorner.CornerRadius = UDim.new(0, 10)
    CloseCorner.Parent = CloseBtn
    
    CloseBtn.MouseButton1Click:Connect(function()
        PlayerListGui:Destroy()
    end)
end

-- CRIAR TODOS OS BOTÕES
createButton("Voar (WASD + Space/Shift)", "✈️", toggleFly, true)
createButton("Atravessar Paredes", "👻", toggleNoclip, true)
createButton("Ver Jogadores (ESP)", "👁️", toggleESP, true)
createButton("Lista de Jogadores", "📋", showPlayerList, false)

createButton("Velocidade Normal", "🚶", function()
    if LocalPlayer.Character and LocalPlayer.Character:FindFirstChildOfClass("Humanoid") then
        LocalPlayer.Character.Humanoid.WalkSpeed = 16
        notify("Velocidade", "Velocidade resetada!", 2)
    end
end, false)

createButton("Velocidade x2", "🏃", function()
    if LocalPlayer.Character and LocalPlayer.Character:FindFirstChildOfClass("Humanoid") then
        LocalPlayer.Character.Humanoid.WalkSpeed = 32
        notify("Velocidade", "Velocidade 2x ativada!", 2)
    end
end, false)

createButton("Velocidade x4", "⚡", function()
    if LocalPlayer.Character and LocalPlayer.Character:FindFirstChildOfClass("Humanoid") then
        LocalPlayer.Character.Humanoid.WalkSpeed = 64
        notify("Velocidade", "Velocidade 4x ativada!", 2)
    end
end, false)

createButton("Velocidade x8 (Extremo)", "🚀", function()
    if LocalPlayer.Character and LocalPlayer.Character:FindFirstChildOfClass("Humanoid") then
        LocalPlayer.Character.Humanoid.WalkSpeed = 128
        notify("Velocidade", "Velocidade 8x ativada!", 2)
    end
end, false)

createButton("Pulo Alto", "🦘", function()
    if LocalPlayer.Character and LocalPlayer.Character:FindFirstChildOfClass("Humanoid") then
        LocalPlayer.Character.Humanoid.JumpPower = 150
        notify("Pulo", "Pulo alto ativado!", 2)
    end
end, false)

createButton("Super Pulo", "🌙", function()
    if LocalPlayer.Character and LocalPlayer.Character:FindFirstChildOfClass("Humanoid") then
        LocalPlayer.Character.Humanoid.JumpPower = 300
        notify("Pulo", "Super pulo ativado!", 2)
    end
end, false)

createButton("Resetar Personagem", "🔄", function()
    if LocalPlayer.Character then
        LocalPlayer.Character:BreakJoints()
        notify("Reset", "Personagem resetado!", 2)
    end
end, false)

createButton("Remover Acessórios", "🎭", function()
    if LocalPlayer.Character then
        for _, v in pairs(LocalPlayer.Character:GetDescendants()) do
            if v:IsA("Accessory") then
                v:Destroy()
            end
        end
        notify("Acessórios", "Removidos com sucesso!", 2)
    end
end, false)

-- Atualizar tamanho do canvas
ScrollFrame.CanvasSize = UDim2.new(0, 0, 0, UIListLayout.AbsoluteContentSize.Y + 10)
UIListLayout:GetPropertyChangedSignal("AbsoluteContentSize"):Connect(function()
    ScrollFrame.CanvasSize = UDim2.new(0, 0, 0, UIListLayout.AbsoluteContentSize.Y + 10)
end)

-- Botão fechar
CloseButton.MouseButton1Click:Connect(function()
    ScreenGui:Destroy()
    if flyConnection then flyConnection:Disconnect() end
    if noclipConnection then noclipConnection:Disconnect() end
    removeESP()
end)

-- Botão minimizar
local minimized = false
MinimizeButton.MouseButton1Click:Connect(function()
    minimized = not minimized
    ScrollFrame.Visible = not minimized
    TweenService:Create(MainFrame, TweenInfo.new(0.3), {
        Size = minimized and UDim2.new(0, 450, 0, 50) or UDim2.new(0, 450, 0, 550)
    }):Play()
end)

-- Notificação inicial
wait(0.5)
notify("🎮 Brookhaven Hub", "Hub carregado com sucesso!", 3)

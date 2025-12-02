-- Brookhaven Script Hub Completo
-- Criado para Roblox Brookhaven

local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local UserInputService = game:GetService("UserInputService")
local TweenService = game:GetService("TweenService")
local LocalPlayer = Players.LocalPlayer
local Camera = workspace.CurrentCamera

-- Variáveis de Estado
local flyEnabled = false
local noclipEnabled = false
local espEnabled = false
local flySpeed = 50
local flyConnection

-- Criar GUI
local ScreenGui = Instance.new("ScreenGui")
local MainFrame = Instance.new("Frame")
local TitleBar = Instance.new("Frame")
local TitleLabel = Instance.new("TextLabel")
local CloseButton = Instance.new("TextButton")
local MinimizeButton = Instance.new("TextButton")
local ScrollFrame = Instance.new("ScrollingFrame")
local UIListLayout = Instance.new("UIListLayout")

-- Configurações da GUI
ScreenGui.Name = "BrookhavenHub"
ScreenGui.Parent = game.CoreGui
ScreenGui.ZIndexBehavior = Enum.ZIndexBehavior.Sibling

MainFrame.Name = "MainFrame"
MainFrame.Parent = ScreenGui
MainFrame.BackgroundColor3 = Color3.fromRGB(35, 35, 45)
MainFrame.BorderSizePixel = 0
MainFrame.Position = UDim2.new(0.3, 0, 0.2, 0)
MainFrame.Size = UDim2.new(0, 400, 0, 500)
MainFrame.Active = true
MainFrame.Draggable = true

-- Arredondar cantos
local MainCorner = Instance.new("UICorner")
MainCorner.CornerRadius = UDim.new(0, 10)
MainCorner.Parent = MainFrame

-- Barra de Título
TitleBar.Name = "TitleBar"
TitleBar.Parent = MainFrame
TitleBar.BackgroundColor3 = Color3.fromRGB(25, 25, 35)
TitleBar.BorderSizePixel = 0
TitleBar.Size = UDim2.new(1, 0, 0, 40)

local TitleCorner = Instance.new("UICorner")
TitleCorner.CornerRadius = UDim.new(0, 10)
TitleCorner.Parent = TitleBar

TitleLabel.Name = "TitleLabel"
TitleLabel.Parent = TitleBar
TitleLabel.BackgroundTransparency = 1
TitleLabel.Position = UDim2.new(0, 10, 0, 0)
TitleLabel.Size = UDim2.new(0.7, 0, 1, 0)
TitleLabel.Font = Enum.Font.GothamBold
TitleLabel.Text = "🏠 Brookhaven Hub"
TitleLabel.TextColor3 = Color3.fromRGB(255, 255, 255)
TitleLabel.TextSize = 18
TitleLabel.TextXAlignment = Enum.TextXAlignment.Left

CloseButton.Name = "CloseButton"
CloseButton.Parent = TitleBar
CloseButton.BackgroundColor3 = Color3.fromRGB(255, 60, 60)
CloseButton.BorderSizePixel = 0
CloseButton.Position = UDim2.new(1, -35, 0.5, -12)
CloseButton.Size = UDim2.new(0, 24, 0, 24)
CloseButton.Font = Enum.Font.GothamBold
CloseButton.Text = "X"
CloseButton.TextColor3 = Color3.fromRGB(255, 255, 255)
CloseButton.TextSize = 14

local CloseCorner = Instance.new("UICorner")
CloseCorner.CornerRadius = UDim.new(0, 12)
CloseCorner.Parent = CloseButton

MinimizeButton.Name = "MinimizeButton"
MinimizeButton.Parent = TitleBar
MinimizeButton.BackgroundColor3 = Color3.fromRGB(255, 180, 60)
MinimizeButton.BorderSizePixel = 0
MinimizeButton.Position = UDim2.new(1, -65, 0.5, -12)
MinimizeButton.Size = UDim2.new(0, 24, 0, 24)
MinimizeButton.Font = Enum.Font.GothamBold
MinimizeButton.Text = "-"
MinimizeButton.TextColor3 = Color3.fromRGB(255, 255, 255)
MinimizeButton.TextSize = 14

local MinCorner = Instance.new("UICorner")
MinCorner.CornerRadius = UDim.new(0, 12)
MinCorner.Parent = MinimizeButton

-- ScrollFrame para botões
ScrollFrame.Name = "ScrollFrame"
ScrollFrame.Parent = MainFrame
ScrollFrame.BackgroundTransparency = 1
ScrollFrame.BorderSizePixel = 0
ScrollFrame.Position = UDim2.new(0, 10, 0, 50)
ScrollFrame.Size = UDim2.new(1, -20, 1, -60)
ScrollFrame.CanvasSize = UDim2.new(0, 0, 0, 0)
ScrollFrame.ScrollBarThickness = 6

UIListLayout.Parent = ScrollFrame
UIListLayout.SortOrder = Enum.SortOrder.LayoutOrder
UIListLayout.Padding = UDim.new(0, 8)

-- Função para criar botões
local function createButton(text, callback)
    local Button = Instance.new("TextButton")
    Button.Parent = ScrollFrame
    Button.BackgroundColor3 = Color3.fromRGB(50, 50, 65)
    Button.BorderSizePixel = 0
    Button.Size = UDim2.new(1, 0, 0, 40)
    Button.Font = Enum.Font.Gotham
    Button.Text = text
    Button.TextColor3 = Color3.fromRGB(255, 255, 255)
    Button.TextSize = 14
    
    local ButtonCorner = Instance.new("UICorner")
    ButtonCorner.CornerRadius = UDim.new(0, 8)
    ButtonCorner.Parent = Button
    
    Button.MouseButton1Click:Connect(callback)
    
    -- Efeito hover
    Button.MouseEnter:Connect(function()
        Button.BackgroundColor3 = Color3.fromRGB(70, 70, 85)
    end)
    Button.MouseLeave:Connect(function()
        Button.BackgroundColor3 = Color3.fromRGB(50, 50, 65)
    end)
    
    return Button
end

-- Função para criar toggle button
local function createToggleButton(text, callback)
    local isEnabled = false
    local Button = createButton(text .. ": OFF", function()
        isEnabled = not isEnabled
        Button.Text = text .. (isEnabled and ": ON" or ": OFF")
        Button.BackgroundColor3 = isEnabled and Color3.fromRGB(60, 180, 75) or Color3.fromRGB(50, 50, 65)
        callback(isEnabled)
    end)
    return Button
end

-- Sistema de FLY
local function toggleFly(enabled)
    flyEnabled = enabled
    
    if flyConnection then
        flyConnection:Disconnect()
    end
    
    if enabled then
        local character = LocalPlayer.Character
        if not character then return end
        
        local humanoidRootPart = character:FindFirstChild("HumanoidRootPart")
        if not humanoidRootPart then return end
        
        local bodyVelocity = Instance.new("BodyVelocity")
        bodyVelocity.Velocity = Vector3.new(0, 0, 0)
        bodyVelocity.MaxForce = Vector3.new(9e9, 9e9, 9e9)
        bodyVelocity.Parent = humanoidRootPart
        
        flyConnection = RunService.Heartbeat:Connect(function()
            if not flyEnabled then
                bodyVelocity:Destroy()
                return
            end
            
            local moveDirection = Vector3.new(0, 0, 0)
            
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
            
            bodyVelocity.Velocity = moveDirection * flySpeed
        end)
    end
end

-- Sistema de NOCLIP
local function toggleNoclip(enabled)
    noclipEnabled = enabled
    
    RunService.Stepped:Connect(function()
        if noclipEnabled then
            local character = LocalPlayer.Character
            if character then
                for _, part in pairs(character:GetDescendants()) do
                    if part:IsA("BasePart") then
                        part.CanCollide = false
                    end
                end
            end
        end
    end)
end

-- Sistema de ESP
local espFolder = Instance.new("Folder")
espFolder.Name = "ESP"
espFolder.Parent = game.CoreGui

local function createESP(player)
    if player == LocalPlayer then return end
    
    local espBox = Instance.new("BillboardGui")
    espBox.Name = player.Name
    espBox.Parent = espFolder
    espBox.AlwaysOnTop = true
    espBox.Size = UDim2.new(0, 100, 0, 50)
    espBox.StudsOffset = Vector3.new(0, 3, 0)
    
    local nameLabel = Instance.new("TextLabel")
    nameLabel.Parent = espBox
    nameLabel.BackgroundTransparency = 0.5
    nameLabel.BackgroundColor3 = Color3.fromRGB(0, 0, 0)
    nameLabel.Size = UDim2.new(1, 0, 1, 0)
    nameLabel.Font = Enum.Font.GothamBold
    nameLabel.Text = player.Name
    nameLabel.TextColor3 = Color3.fromRGB(255, 255, 255)
    nameLabel.TextSize = 14
    nameLabel.TextStrokeTransparency = 0
    
    local function updateESP()
        if player.Character and player.Character:FindFirstChild("HumanoidRootPart") then
            espBox.Adornee = player.Character.HumanoidRootPart
            local distance = (LocalPlayer.Character.HumanoidRootPart.Position - player.Character.HumanoidRootPart.Position).Magnitude
            nameLabel.Text = player.Name .. "\n[" .. math.floor(distance) .. " studs]"
        end
    end
    
    RunService.RenderStepped:Connect(updateESP)
end

local function toggleESP(enabled)
    espEnabled = enabled
    
    if enabled then
        for _, player in pairs(Players:GetPlayers()) do
            createESP(player)
        end
        
        Players.PlayerAdded:Connect(function(player)
            if espEnabled then
                createESP(player)
            end
        end)
    else
        espFolder:ClearAllChildren()
    end
end

-- Função de Teleporte
local function teleportToPlayer(targetPlayer)
    local character = LocalPlayer.Character
    local targetCharacter = targetPlayer.Character
    
    if character and targetCharacter then
        local hrp = character:FindFirstChild("HumanoidRootPart")
        local targetHrp = targetCharacter:FindFirstChild("HumanoidRootPart")
        
        if hrp and targetHrp then
            hrp.CFrame = targetHrp.CFrame * CFrame.new(0, 0, 3)
        end
    end
end

-- Função para puxar jogador
local function bringPlayer(targetPlayer)
    local character = LocalPlayer.Character
    local targetCharacter = targetPlayer.Character
    
    if character and targetCharacter then
        local hrp = character:FindFirstChild("HumanoidRootPart")
        local targetHrp = targetCharacter:FindFirstChild("HumanoidRootPart")
        
        if hrp and targetHrp then
            targetHrp.CFrame = hrp.CFrame * CFrame.new(0, 0, -3)
        end
    end
end

-- Lista de jogadores online
local function showOnlinePlayers()
    local playerList = ""
    for _, player in pairs(Players:GetPlayers()) do
        playerList = playerList .. player.Name .. "\n"
    end
    
    local NotifGui = Instance.new("ScreenGui")
    NotifGui.Parent = game.CoreGui
    
    local NotifFrame = Instance.new("Frame")
    NotifFrame.Parent = NotifGui
    NotifFrame.BackgroundColor3 = Color3.fromRGB(35, 35, 45)
    NotifFrame.BorderSizePixel = 0
    NotifFrame.Position = UDim2.new(0.5, -150, 0.5, -200)
    NotifFrame.Size = UDim2.new(0, 300, 0, 400)
    
    local Corner = Instance.new("UICorner")
    Corner.CornerRadius = UDim.new(0, 10)
    Corner.Parent = NotifFrame
    
    local Title = Instance.new("TextLabel")
    Title.Parent = NotifFrame
    Title.BackgroundTransparency = 1
    Title.Size = UDim2.new(1, 0, 0, 40)
    Title.Font = Enum.Font.GothamBold
    Title.Text = "Jogadores Online"
    Title.TextColor3 = Color3.fromRGB(255, 255, 255)
    Title.TextSize = 18
    
    local List = Instance.new("TextLabel")
    List.Parent = NotifFrame
    List.BackgroundTransparency = 1
    List.Position = UDim2.new(0, 10, 0, 50)
    List.Size = UDim2.new(1, -20, 1, -100)
    List.Font = Enum.Font.Gotham
    List.Text = playerList
    List.TextColor3 = Color3.fromRGB(255, 255, 255)
    List.TextSize = 14
    List.TextYAlignment = Enum.TextYAlignment.Top
    
    local CloseBtn = Instance.new("TextButton")
    CloseBtn.Parent = NotifFrame
    CloseBtn.BackgroundColor3 = Color3.fromRGB(255, 60, 60)
    CloseBtn.Position = UDim2.new(0.5, -50, 1, -50)
    CloseBtn.Size = UDim2.new(0, 100, 0, 35)
    CloseBtn.Font = Enum.Font.GothamBold
    CloseBtn.Text = "Fechar"
    CloseBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
    CloseBtn.TextSize = 14
    
    local CloseCorner = Instance.new("UICorner")
    CloseCorner.CornerRadius = UDim.new(0, 8)
    CloseCorner.Parent = CloseBtn
    
    CloseBtn.MouseButton1Click:Connect(function()
        NotifGui:Destroy()
    end)
    
    wait(10)
    if NotifGui then NotifGui:Destroy() end
end

-- Criar botões na interface
createToggleButton("✈️ Fly (WASD + Space/Shift)", toggleFly)
createToggleButton("👻 Noclip", toggleNoclip)
createToggleButton("👁️ ESP (Ver Jogadores)", toggleESP)
createButton("📋 Ver Jogadores Online", showOnlinePlayers)
createButton("🏃 Velocidade x2", function()
    if LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("Humanoid") then
        LocalPlayer.Character.Humanoid.WalkSpeed = 32
    end
end)
createButton("⚡ Velocidade x4", function()
    if LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("Humanoid") then
        LocalPlayer.Character.Humanoid.WalkSpeed = 64
    end
end)
createButton("🦘 Pulo Alto", function()
    if LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("Humanoid") then
        LocalPlayer.Character.Humanoid.JumpPower = 120
    end
end)
createButton("🔄 Resetar Stats", function()
    if LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("Humanoid") then
        LocalPlayer.Character.Humanoid.WalkSpeed = 16
        LocalPlayer.Character.Humanoid.JumpPower = 50
    end
end)

-- Teleport para jogadores específicos
createButton("📍 Teleportar para Jogador", function()
    local playerName = game:GetService("UserInputService"):GetStringForKeyCode(Enum.KeyCode.Unknown)
    -- Aqui você pode adicionar um input box para digitar o nome
    for _, player in pairs(Players:GetPlayers()) do
        if player ~= LocalPlayer then
            teleportToPlayer(player)
            break
        end
    end
end)

-- Atualizar tamanho do canvas
ScrollFrame.CanvasSize = UDim2.new(0, 0, 0, UIListLayout.AbsoluteContentSize.Y + 10)
UIListLayout:GetPropertyChangedSignal("AbsoluteContentSize"):Connect(function()
    ScrollFrame.CanvasSize = UDim2.new(0, 0, 0, UIListLayout.AbsoluteContentSize.Y + 10)
end)

-- Botões de fechar e minimizar
CloseButton.MouseButton1Click:Connect(function()
    ScreenGui:Destroy()
end)

local minimized = false
MinimizeButton.MouseButton1Click:Connect(function()
    minimized = not minimized
    ScrollFrame.Visible = not minimized
    MainFrame.Size = minimized and UDim2.new(0, 400, 0, 40) or UDim2.new(0, 400, 0, 500)
end)

-- Notificação de carregamento
local function notify(text)
    game.StarterGui:SetCore("SendNotification", {
        Title = "Brookhaven Hub";
        Text = text;
        Duration = 3;
    })
end

notify("Hub carregado com sucesso!")
print("Brookhaven Script Hub - Ativado!")

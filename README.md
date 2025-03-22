--// Spock Land Aimbot v1.0 Completo com UI e Toggle GUI
-- Criado por ChatGPT para uso educacional

-- Serviços
local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local UserInputService = game:GetService("UserInputService")
local LocalPlayer = Players.LocalPlayer
local Camera = workspace.CurrentCamera

-- Configuração Inicial
local Settings = {
    Enabled = false,
    FOV = 100,
    CircleColor = Color3.fromRGB(0, 255, 255),
    AimPart = "Head",
    AimKey = Enum.UserInputType.MouseButton2
}

-- Círculo FOV
local FOVCircle = Drawing.new("Circle")
FOVCircle.Radius = Settings.FOV
FOVCircle.Thickness = 2
FOVCircle.Position = Vector2.new(Camera.ViewportSize.X/2, Camera.ViewportSize.Y/2)
FOVCircle.Color = Settings.CircleColor
FOVCircle.Visible = true
FOVCircle.Filled = false

-- Função de Alvo
local function GetClosestPlayer()
    local closest = nil
    local shortestDistance = Settings.FOV

    for _, player in ipairs(Players:GetPlayers()) do
        if player ~= LocalPlayer and player.Character and player.Character:FindFirstChild(Settings.AimPart) then
            local partPos = player.Character[Settings.AimPart].Position
            local screenPos, onScreen = Camera:WorldToViewportPoint(partPos)
            if onScreen then
                local dist = (Vector2.new(screenPos.X, screenPos.Y) - Vector2.new(Camera.ViewportSize.X/2, Camera.ViewportSize.Y/2)).Magnitude
                if dist < shortestDistance then
                    closest = player
                    shortestDistance = dist
                end
            end
        end
    end

    return closest
end

-- Mira Automática
RunService.RenderStepped:Connect(function()
    FOVCircle.Radius = Settings.FOV
    FOVCircle.Color = Settings.CircleColor
    FOVCircle.Position = Vector2.new(Camera.ViewportSize.X / 2, Camera.ViewportSize.Y / 2)

    if Settings.Enabled then
        local target = GetClosestPlayer()
        if target and target.Character and target.Character:FindFirstChild(Settings.AimPart) then
            local targetPos = target.Character[Settings.AimPart].Position
            Camera.CFrame = CFrame.new(Camera.CFrame.Position, targetPos)
        end
    end
end)

-- Tecla de Aimbot
UserInputService.InputBegan:Connect(function(input, processed)
    if input.UserInputType == Settings.AimKey then
        Settings.Enabled = true
    end
end)

UserInputService.InputEnded:Connect(function(input, processed)
    if input.UserInputType == Settings.AimKey then
        Settings.Enabled = false
    end
end)

-- GUI Principal
local ScreenGui = Instance.new("ScreenGui", LocalPlayer:WaitForChild("PlayerGui"))
ScreenGui.Name = "SpockLandAimbotUI"
ScreenGui.ResetOnSpawn = false

-- Frame Principal
local Frame = Instance.new("Frame", ScreenGui)
Frame.Size = UDim2.new(0, 300, 0, 250)
Frame.Position = UDim2.new(0.05, 0, 0.3, 0)
Frame.BackgroundColor3 = Color3.fromRGB(20, 20, 20)
Frame.BorderSizePixel = 0
Frame.Visible = true
Frame.Name = "MainFrame"

-- Título
local Title = Instance.new("TextLabel", Frame)
Title.Size = UDim2.new(1, 0, 0, 40)
Title.Text = "Spock Land Aimbot"
Title.TextColor3 = Color3.fromRGB(0, 255, 255)
Title.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
Title.Font = Enum.Font.GothamBold
Title.TextSize = 20

-- Toggle Aimbot
local ToggleAimbot = Instance.new("TextButton", Frame)
ToggleAimbot.Size = UDim2.new(1, -20, 0, 30)
ToggleAimbot.Position = UDim2.new(0, 10, 0, 60)
ToggleAimbot.Text = "Ativar Aimbot: OFF"
ToggleAimbot.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
ToggleAimbot.TextColor3 = Color3.fromRGB(255, 255, 255)
ToggleAimbot.Font = Enum.Font.Gotham
ToggleAimbot.TextSize = 16

-- Campo FOV
local FOVSlider = Instance.new("TextBox", Frame)
FOVSlider.Size = UDim2.new(1, -20, 0, 30)
FOVSlider.Position = UDim2.new(0, 10, 0, 100)
FOVSlider.Text = "FOV: "..Settings.FOV
FOVSlider.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
FOVSlider.TextColor3 = Color3.fromRGB(255, 255, 255)
FOVSlider.Font = Enum.Font.Gotham
FOVSlider.TextSize = 16
FOVSlider.ClearTextOnFocus = true

-- Cor RGB
local ColorBox = Instance.new("TextBox", Frame)
ColorBox.Size = UDim2.new(1, -20, 0, 30)
ColorBox.Position = UDim2.new(0, 10, 0, 140)
ColorBox.Text = "Cor (ex: 255,0,255)"
ColorBox.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
ColorBox.TextColor3 = Color3.fromRGB(255, 255, 255)
ColorBox.Font = Enum.Font.Gotham
ColorBox.TextSize = 16

-- Botão Abrir/Fechar GUI
local ToggleGUIBtn = Instance.new("ImageButton", ScreenGui)
ToggleGUIBtn.Size = UDim2.new(0, 40, 0, 40)
ToggleGUIBtn.Position = UDim2.new(0, 10, 0, 10)
ToggleGUIBtn.BackgroundTransparency = 1
ToggleGUIBtn.Image = "rbxassetid://79223736969893"
ToggleGUIBtn.Name = "ToggleGUIButton"

ToggleGUIBtn.MouseButton1Click:Connect(function()
    Frame.Visible = not Frame.Visible
end)

-- Funções dos Inputs
ToggleAimbot.MouseButton1Click:Connect(function()
    Settings.Enabled = not Settings.Enabled
    ToggleAimbot.Text = "Ativar Aimbot: "..(Settings.Enabled and "ON" or "OFF")
end)

FOVSlider.FocusLost:Connect(function()
    local value = tonumber(FOVSlider.Text:match("%d+"))
    if value then
        Settings.FOV = math.clamp(value, 20, 500)
        FOVSlider.Text = "FOV: "..Settings.FOV
    end
end)

ColorBox.FocusLost:Connect(function()
    local r, g, b = ColorBox.Text:match("(%d+),(%d+),(%d+)")
    if r and g and b then
        Settings.CircleColor = Color3.fromRGB(tonumber(r), tonumber(g), tonumber(b))
    end
end)

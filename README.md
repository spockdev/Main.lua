local HttpService = game:GetService("HttpService")
local Fluent = loadstring(game:HttpGet("https://raw.githubusercontent.com/Stefanuk12/Fluent/main/UILib.lua"))()

-- Sua API do Panda Dev
local PandaKeyAPI = "https://pandadev.xyz/auth/check.php?key=4466c8cc4ab05ecf9fc755016f41ddcfccca1a030f668b5b6601e57eeed3b975"

local userKey = ""  -- Variável para armazenar a chave do usuário

-- Função para validar a chave
local function checkKey(key)
    local success, response = pcall(function()
        return HttpService:GetAsync(PandaKeyAPI .. key)  -- Faz a requisição para validar a chave
    end)

    if success and response == "Valid" then
        return true  -- Chave válida
    else
        return false  -- Chave inválida
    end
end

-- Função para criar a UI de Login
local function createLoginUI()
    local LoginWindow = Fluent:CreateWindow({
        Title = "Spock Land Hub",
        SubTitle = "Sistema de Key - Panda Dev",
        TabWidth = 150,
        Size = UDim2.fromOffset(400, 250),
        Acrylic = true,
        Theme = "Red"
    })

    local LoginTab = LoginWindow:AddTab({ Title = "Login", Icon = "lock" })

    -- Caixa de texto para inserir a chave
    local KeyBox = LoginTab:AddTextbox("Digite sua Key", { Default = "", ClearTextOnFocus = true }, function(value)
        userKey = value  -- A chave inserida pelo usuário será armazenada aqui
    end)

    -- Botão para verificar a chave
    LoginTab:AddButton("Verificar Key", function()
        local isValid = checkKey(userKey)

        if isValid then
            LoginWindow:SetVisible(false)  -- Fecha a janela de login
            loadAimbotUI()  -- Chama a função para carregar a interface do Aimbot
        else
            LoginTab:AddParagraph("Erro", "Key inválida, tente novamente.")  -- Mensagem de erro
        end
    end)

    LoginWindow:SelectTab(1)  -- Seleciona a primeira aba
end

-- Função para carregar a UI principal após a chave ser validada
function loadAimbotUI()
    local Window = Fluent:CreateWindow({
        Title = "Spock Land Hub - Aimbot Universal",
        SubTitle = "By Spock",
        TabWidth = 150,
        Size = UDim2.fromOffset(500, 350),
        Acrylic = true,
        Theme = "Red"
    })

    -- 🎯 Página Principal (Aimbot)
    local MainTab = Window:AddTab({ Title = "Aimbot", Icon = "skull" })
    local AimEnabled, AimRadius, AimSmoothness, AimKey, TargetPart, OnlyVisible = false, 150, 5, Enum.KeyCode.E, "Head", false

    MainTab:AddToggle("Ativar Aimbot", { Default = false }, function(state) AimEnabled = state end)
    MainTab:AddKeybind("Tecla de Aimbot", { Default = "E" }, function(key) AimKey = key end)

    -- 🎮 Página de Configuração
    local ConfigTab = Window:AddTab({ Title = "Configurações", Icon = "settings" })
    ConfigTab:AddDropdown("Prioridade de Mira", { Options = { "Head", "Torso" }, Default = "Head" }, function(value) TargetPart = value end)
    ConfigTab:AddToggle("Apenas Visíveis", { Default = false }, function(state) OnlyVisible = state end)
    ConfigTab:AddSlider("Raio de Mira", { Min = 50, Max = 300, Default = 150 }, function(value) AimRadius = value end)
    ConfigTab:AddSlider("Suavidade", { Min = 1, Max = 10, Default = 5 }, function(value) AimSmoothness = value end)

    -- 📜 Página de Créditos
    local CreditsTab = Window:AddTab({ Title = "Créditos", Icon = "heart" })
    CreditsTab:AddParagraph("Criado por:", "WaveAI & SPDM Team")
    CreditsTab:AddParagraph("Biblioteca UI:", "FluentLib by Stefanuk12")

    -- 🔘 Botão de Abrir/Fechar UI
    local Button = Instance.new("ImageButton")
    Button.Size, Button.Position, Button.BackgroundTransparency, Button.Image = UDim2.new(0, 50, 0, 50), UDim2.new(0, 10, 0, 10), 1, "rbxassetid://121819461851506"
    Button.Parent = game.CoreGui
    local isOpen = true
    Button.MouseButton1Click:Connect(function()
        isOpen = not isOpen
        Window:SetVisible(isOpen)
    end)

    Window:SelectTab(1)  -- Seleciona a primeira aba
end

-- Chama a função para criar a UI de login quando o jogo iniciar
createLoginUI()

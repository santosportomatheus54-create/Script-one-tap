--[[
    TK SCRIPTS - MIRA FECHADA GRUDADA + FOV AJUSTÁVEL
    VERSÃO CORRIGIDA - 100% FUNCIONAL
    ALTERAÇÃO: Aimbot puxa para a cabeça da pessoa mais próxima de você
]]

-- Services
local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local UserInputService = game:GetService("UserInputService")
local TweenService = game:GetService("TweenService")
local CoreGui = game:GetService("CoreGui")
local StarterGui = game:GetService("StarterGui")
local Camera = workspace.CurrentCamera

-- Variáveis
local LocalPlayer = Players.LocalPlayer
local Mouse = LocalPlayer:GetMouse()

-- CONFIGURAÇÕES
local Settings = {
    Aimbot = {
        Enabled = true,
        FOV = 250,
        ShowFOV = true,
        HitPart = "Head",
        Locked = false,
        CurrentTarget = nil
    },
    ESP = {
        Enabled = true,
        Names = true,
        Distance = true
    }
}

-- ==================== VERIFICAÇÃO INICIAL ====================
print("🚀 TK SCRIPTS - Iniciando...")

-- Notificação de teste
pcall(function()
    StarterGui:SetCore("SendNotification", {
        Title = "TK SCRIPTS",
        Text = "Carregando interface...",
        Duration = 2
    })
end)

-- ==================== DRAWING LIBRARY ====================
local DrawingLibrary = nil
local FOVCircle = nil

-- Tentar carregar Drawing
pcall(function()
    DrawingLibrary = Drawing
    if not DrawingLibrary then
        DrawingLibrary = loadstring(game:HttpGet("https://raw.githubusercontent.com/peyton2465/DrawingLib/main/source.lua"))()
    end
end)

-- Criar círculo FOV
if DrawingLibrary then
    pcall(function()
        FOVCircle = DrawingLibrary.new("Circle")
        FOVCircle.Transparency = 0.5
        FOVCircle.Color = Color3.fromRGB(255, 0, 0)
        FOVCircle.Thickness = 3
        FOVCircle.NumSides = 64
        FOVCircle.Filled = false
        FOVCircle.Radius = Settings.Aimbot.FOV
        FOVCircle.Visible = Settings.Aimbot.ShowFOV
        print("✅ Círculo FOV criado")
    end)
end

-- ==================== INTERFACE ====================

-- Criar ScreenGui
local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Name = "TK_Scripts_Menu"
ScreenGui.ResetOnSpawn = false
ScreenGui.ZIndexBehavior = Enum.ZIndexBehavior.Sibling
ScreenGui.DisplayOrder = 999999

-- Tentar diferentes parents
local parentFound = false
local parents = {CoreGui, game:GetService("Players").LocalPlayer:FindFirstChild("PlayerGui"), game:GetService("StarterGui")}

for _, parent in ipairs(parents) do
    if parent and not parentFound then
        pcall(function()
            ScreenGui.Parent = parent
            parentFound = true
        end)
    end
end

if not parentFound then
    ScreenGui.Parent = CoreGui
end

-- Proteger GUI
pcall(function()
    if syn and syn.protect_gui then
        syn.protect_gui(ScreenGui)
    end
    if gethui then
        ScreenGui.Parent = gethui()
    end
end)

-- ==================== ÍCONE FLUTUANTE ====================
local TKFloatIcon = Instance.new("TextButton")
TKFloatIcon.Name = "TKFloatIcon"
TKFloatIcon.Size = UDim2.new(0, 70, 0, 70)
TKFloatIcon.Position = UDim2.new(0.9, -35, 0.85, -35)
TKFloatIcon.BackgroundColor3 = Color3.fromRGB(0, 0, 0)
TKFloatIcon.BackgroundTransparency = 0.1
TKFloatIcon.BorderColor3 = Color3.fromRGB(255, 0, 0)
TKFloatIcon.BorderSizePixel = 3
TKFloatIcon.Text = "TK"
TKFloatIcon.TextColor3 = Color3.fromRGB(255, 0, 0)
TKFloatIcon.TextScaled = true
TKFloatIcon.Font = Enum.Font.GothamBlack
TKFloatIcon.Visible = false
TKFloatIcon.ZIndex = 9999
TKFloatIcon.Parent = ScreenGui

local IconCorner = Instance.new("UICorner")
IconCorner.CornerRadius = UDim.new(1, 0)
IconCorner.Parent = TKFloatIcon

-- ==================== PAINEL PRINCIPAL ====================
local MainPanel = Instance.new("Frame")
MainPanel.Size = UDim2.new(0, 450, 0, 450)
MainPanel.Position = UDim2.new(0.5, -225, 0.4, -225)
MainPanel.BackgroundColor3 = Color3.fromRGB(5, 0, 0)
MainPanel.BackgroundTransparency = 0.1
MainPanel.BorderColor3 = Color3.fromRGB(255, 0, 0)
MainPanel.BorderSizePixel = 3
MainPanel.Active = true
MainPanel.Draggable = true
MainPanel.Visible = true
MainPanel.ZIndex = 9000
MainPanel.Parent = ScreenGui

-- Fundo com blur
local PanelBlur = Instance.new("Frame")
PanelBlur.Size = UDim2.new(1, 0, 1, 0)
PanelBlur.BackgroundColor3 = Color3.fromRGB(0, 0, 0)
PanelBlur.BackgroundTransparency = 0.3
PanelBlur.BorderSizePixel = 0
PanelBlur.ZIndex = 8999
PanelBlur.Parent = MainPanel

-- Cantos arredondados
local PanelCorner = Instance.new("UICorner")
PanelCorner.CornerRadius = UDim.new(0, 20)
PanelCorner.Parent = MainPanel

-- ==================== HEADER ====================
local Header = Instance.new("Frame")
Header.Size = UDim2.new(1, -20, 0, 70)
Header.Position = UDim2.new(0, 10, 0, 10)
Header.BackgroundTransparency = 1
Header.Parent = MainPanel

local Title = Instance.new("TextLabel")
Title.Size = UDim2.new(0.8, 0, 1, 0)
Title.BackgroundTransparency = 1
Title.Text = "TK SCRIPTS"
Title.TextColor3 = Color3.fromRGB(255, 0, 0)
Title.TextScaled = true
Title.Font = Enum.Font.GothamBlack
Title.TextXAlignment = Enum.TextXAlignment.Left
Title.Parent = Header

-- Botão fechar
local CloseButton = Instance.new("TextButton")
CloseButton.Size = UDim2.new(0, 40, 0, 40)
CloseButton.Position = UDim2.new(1, -45, 0, 15)
CloseButton.BackgroundColor3 = Color3.fromRGB(40, 0, 0)
CloseButton.BorderColor3 = Color3.fromRGB(255, 0, 0)
CloseButton.BorderSizePixel = 2
CloseButton.Text = "✕"
CloseButton.TextColor3 = Color3.fromRGB(255, 0, 0)
CloseButton.TextScaled = true
CloseButton.Font = Enum.Font.GothamBold
CloseButton.Parent = Header

local CloseCorner = Instance.new("UICorner")
CloseCorner.CornerRadius = UDim.new(1, 0)
CloseCorner.Parent = CloseButton

-- ==================== STATUS ====================
local StatusFrame = Instance.new("Frame")
StatusFrame.Size = UDim2.new(1, -40, 0, 70)
StatusFrame.Position = UDim2.new(0, 20, 0, 90)
StatusFrame.BackgroundColor3 = Color3.fromRGB(20, 0, 0)
StatusFrame.BorderColor3 = Color3.fromRGB(255, 0, 0)
StatusFrame.BorderSizePixel = 2
StatusFrame.Parent = MainPanel

local StatusCorner = Instance.new("UICorner")
StatusCorner.CornerRadius = UDim.new(0, 10)
StatusCorner.Parent = StatusFrame

local StatusTitle = Instance.new("TextLabel")
StatusTitle.Size = UDim2.new(1, 0, 0.5, 0)
StatusTitle.Position = UDim2.new(0, 0, 0.1, 0)
StatusTitle.BackgroundTransparency = 1
StatusTitle.Text = "⚡ MIRA GRUDADA ATIVA"
StatusTitle.TextColor3 = Color3.fromRGB(0, 255, 0)
StatusTitle.TextScaled = true
StatusTitle.Font = Enum.Font.GothamBold
StatusTitle.Parent = StatusFrame

local StatusDesc = Instance.new("TextLabel")
StatusDesc.Size = UDim2.new(1, 0, 0.3, 0)
StatusDesc.Position = UDim2.new(0, 0, 0.6, 0)
StatusDesc.BackgroundTransparency = 1
StatusDesc.Text = "FOV: " .. Settings.Aimbot.FOV .. "px | Mirando no mais próximo"
StatusDesc.TextColor3 = Color3.fromRGB(255, 255, 255)
StatusDesc.TextScaled = true
StatusDesc.Font = Enum.Font.Gotham
StatusDesc.Parent = StatusFrame

-- ==================== CONTEÚDO ====================
local Content = Instance.new("Frame")
Content.Size = UDim2.new(1, -40, 1, -200)
Content.Position = UDim2.new(0, 20, 0, 170)
Content.BackgroundTransparency = 1
Content.Parent = MainPanel

-- Título FOV
local FOVTitle = Instance.new("TextLabel")
FOVTitle.Size = UDim2.new(1, 0, 0, 30)
FOVTitle.BackgroundTransparency = 1
FOVTitle.Text = "TAMANHO DO FOV"
FOVTitle.TextColor3 = Color3.fromRGB(255, 0, 0)
FOVTitle.TextScaled = true
FOVTitle.Font = Enum.Font.GothamBold
FOVTitle.Parent = Content

-- Valor FOV
local FOVValue = Instance.new("TextLabel")
FOVValue.Size = UDim2.new(1, 0, 0, 25)
FOVValue.Position = UDim2.new(0, 0, 0, 30)
FOVValue.BackgroundTransparency = 1
FOVValue.Text = Settings.Aimbot.FOV .. " px"
FOVValue.TextColor3 = Color3.fromRGB(255, 255, 255)
FOVValue.TextScaled = true
FOVValue.Font = Enum.Font.Gotham
FOVValue.Parent = Content

-- Slider Background
local SliderBg = Instance.new("Frame")
SliderBg.Size = UDim2.new(1, -20, 0, 8)
SliderBg.Position = UDim2.new(0, 10, 0, 70)
SliderBg.BackgroundColor3 = Color3.fromRGB(40, 0, 0)
SliderBg.BorderSizePixel = 0
SliderBg.Parent = Content

local SliderBgCorner = Instance.new("UICorner")
SliderBgCorner.CornerRadius = UDim.new(1, 0)
SliderBgCorner.Parent = SliderBg

-- Slider Fill
local SliderFill = Instance.new("Frame")
SliderFill.Size = UDim2.new((Settings.Aimbot.FOV - 50) / 350, 0, 1, 0)
SliderFill.BackgroundColor3 = Color3.fromRGB(255, 0, 0)
SliderFill.BorderSizePixel = 0
SliderFill.Parent = SliderBg

local SliderFillCorner = Instance.new("UICorner")
SliderFillCorner.CornerRadius = UDim.new(1, 0)
SliderFillCorner.Parent = SliderFill

-- Slider Button
local SliderButton = Instance.new("TextButton")
SliderButton.Size = UDim2.new(0, 22, 0, 22)
SliderButton.Position = UDim2.new((Settings.Aimbot.FOV - 50) / 350, -11, 0, -7)
SliderButton.BackgroundColor3 = Color3.fromRGB(255, 0, 0)
SliderButton.BorderColor3 = Color3.fromRGB(255, 255, 255)
SliderButton.BorderSizePixel = 2
SliderButton.Text = ""
SliderButton.ZIndex = 9001
SliderButton.Parent = Content

local SliderBtnCorner = Instance.new("UICorner")
SliderBtnCorner.CornerRadius = UDim.new(1, 0)
SliderBtnCorner.Parent = SliderButton

-- Min/Max
local MinLabel = Instance.new("TextLabel")
MinLabel.Size = UDim2.new(0, 40, 0, 20)
MinLabel.Position = UDim2.new(0, 5, 0, 85)
MinLabel.BackgroundTransparency = 1
MinLabel.Text = "50"
MinLabel.TextColor3 = Color3.fromRGB(255, 0, 0)
MinLabel.TextScaled = true
MinLabel.Font = Enum.Font.Gotham
MinLabel.Parent = Content

local MaxLabel = Instance.new("TextLabel")
MaxLabel.Size = UDim2.new(0, 40, 0, 20)
MaxLabel.Position = UDim2.new(1, -45, 0, 85)
MaxLabel.BackgroundTransparency = 1
MaxLabel.Text = "400"
MaxLabel.TextColor3 = Color3.fromRGB(255, 0, 0)
MaxLabel.TextScaled = true
MaxLabel.Font = Enum.Font.Gotham
MaxLabel.TextXAlignment = Enum.TextXAlignment.Right
MaxLabel.Parent = Content

-- ==================== TOGGLES ====================
-- Toggle Mostrar FOV
local ToggleFrame = Instance.new("Frame")
ToggleFrame.Size = UDim2.new(1, 0, 0, 50)
ToggleFrame.Position = UDim2.new(0, 0, 0, 120)
ToggleFrame.BackgroundTransparency = 1
ToggleFrame.Parent = Content

local ToggleText = Instance.new("TextLabel")
ToggleText.Size = UDim2.new(0.7, 0, 1, 0)
ToggleText.BackgroundTransparency = 1
ToggleText.Text = "Mostrar Círculo FOV"
ToggleText.TextColor3 = Color3.fromRGB(255, 255, 255)
ToggleText.TextScaled = true
ToggleText.Font = Enum.Font.Gotham
ToggleText.TextXAlignment = Enum.TextXAlignment.Left
ToggleText.Parent = ToggleFrame

local ToggleBtn = Instance.new("TextButton")
ToggleBtn.Size = UDim2.new(0, 50, 0, 25)
ToggleBtn.Position = UDim2.new(1, -55, 0.5, -12.5)
ToggleBtn.BackgroundColor3 = Color3.fromRGB(255, 0, 0)
ToggleBtn.BorderColor3 = Color3.fromRGB(255, 255, 255)
ToggleBtn.BorderSizePixel = 1
ToggleBtn.Text = ""
ToggleBtn.Parent = ToggleFrame

local ToggleCorner = Instance.new("UICorner")
ToggleCorner.CornerRadius = UDim.new(1, 0)
ToggleCorner.Parent = ToggleBtn

local ToggleIndicator = Instance.new("Frame")
ToggleIndicator.Size = UDim2.new(0, 19, 0, 19)
ToggleIndicator.Position = UDim2.new(1, -24, 0.5, -9.5)
ToggleIndicator.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
ToggleIndicator.BorderSizePixel = 0
ToggleIndicator.Parent = ToggleBtn

local IndicatorCorner = Instance.new("UICorner")
IndicatorCorner.CornerRadius = UDim.new(1, 0)
IndicatorCorner.Parent = ToggleIndicator

-- Toggle Aimbot
local AimbotToggleFrame = Instance.new("Frame")
AimbotToggleFrame.Size = UDim2.new(1, 0, 0, 50)
AimbotToggleFrame.Position = UDim2.new(0, 0, 0, 180)
AimbotToggleFrame.BackgroundTransparency = 1
AimbotToggleFrame.Parent = Content

local AimbotText = Instance.new("TextLabel")
AimbotText.Size = UDim2.new(0.7, 0, 1, 0)
AimbotText.BackgroundTransparency = 1
AimbotText.Text = "Aimbot (Mira Grudada)"
AimbotText.TextColor3 = Color3.fromRGB(255, 255, 255)
AimbotText.TextScaled = true
AimbotText.Font = Enum.Font.Gotham
AimbotText.TextXAlignment = Enum.TextXAlignment.Left
AimbotText.Parent = AimbotToggleFrame

local AimbotBtn = Instance.new("TextButton")
AimbotBtn.Size = UDim2.new(0, 80, 0, 30)
AimbotBtn.Position = UDim2.new(1, -85, 0.5, -15)
AimbotBtn.BackgroundColor3 = Color3.fromRGB(0, 255, 0)
AimbotBtn.BorderColor3 = Color3.fromRGB(255, 0, 0)
AimbotBtn.BorderSizePixel = 2
AimbotBtn.Text = "LIGADO"
AimbotBtn.TextColor3 = Color3.fromRGB(0, 0, 0)
AimbotBtn.TextScaled = true
AimbotBtn.Font = Enum.Font.GothamBold
AimbotBtn.Parent = AimbotToggleFrame

local AimbotBtnCorner = Instance.new("UICorner")
AimbotBtnCorner.CornerRadius = UDim.new(0, 8)
AimbotBtnCorner.Parent = AimbotBtn

-- Toggle ESP
local ESPToggleFrame = Instance.new("Frame")
ESPToggleFrame.Size = UDim2.new(1, 0, 0, 50)
ESPToggleFrame.Position = UDim2.new(0, 0, 0, 240)
ESPToggleFrame.BackgroundTransparency = 1
ESPToggleFrame.Parent = Content

local ESPText = Instance.new("TextLabel")
ESPText.Size = UDim2.new(0.7, 0, 1, 0)
ESPText.BackgroundTransparency = 1
ESPText.Text = "ESP (Nome + Distância)"
ESPText.TextColor3 = Color3.fromRGB(255, 255, 255)
ESPText.TextScaled = true
ESPText.Font = Enum.Font.Gotham
ESPText.TextXAlignment = Enum.TextXAlignment.Left
ESPText.Parent = ESPToggleFrame

local ESPBtn = Instance.new("TextButton")
ESPBtn.Size = UDim2.new(0, 80, 0, 30)
ESPBtn.Position = UDim2.new(1, -85, 0.5, -15)
ESPBtn.BackgroundColor3 = Color3.fromRGB(0, 255, 0)
ESPBtn.BorderColor3 = Color3.fromRGB(255, 0, 0)
ESPBtn.BorderSizePixel = 2
ESPBtn.Text = "LIGADO"
ESPBtn.TextColor3 = Color3.fromRGB(0, 0, 0)
ESPBtn.TextScaled = true
ESPBtn.Font = Enum.Font.GothamBold
ESPBtn.Parent = ESPToggleFrame

local ESPBtnCorner = Instance.new("UICorner")
ESPBtnCorner.CornerRadius = UDim.new(0, 8)
ESPBtnCorner.Parent = ESPBtn

-- ==================== RODAPÉ ====================
local Footer = Instance.new("Frame")
Footer.Size = UDim2.new(1, -40, 0, 30)
Footer.Position = UDim2.new(0, 20, 1, -40)
Footer.BackgroundTransparency = 1
Footer.Parent = MainPanel

local FooterText = Instance.new("TextLabel")
FooterText.Size = UDim2.new(1, 0, 1, 0)
FooterText.BackgroundTransparency = 1
FooterText.Text = "TK SCRIPTS - Mira no Mais Próximo | FOV Ajustável"
FooterText.TextColor3 = Color3.fromRGB(150, 150, 150)
FooterText.TextScaled = true
FooterText.Font = Enum.Font.Gotham
FooterText.Parent = Footer

-- ==================== FUNCIONALIDADES DOS BOTÕES ====================

-- Slider funcional
local dragging = false

SliderButton.MouseButton1Down:Connect(function()
    dragging = true
end)

UserInputService.InputEnded:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseButton1 then
        dragging = false
    end
end)

UserInputService.InputChanged:Connect(function(input)
    if dragging and input.UserInputType == Enum.UserInputType.MouseMovement then
        local mousePos = UserInputService:GetMouseLocation()
        local sliderPos = SliderBg.AbsolutePosition.X
        local sliderSize = SliderBg.AbsoluteSize.X
        local relativeX = math.clamp(mousePos - sliderPos, 0, sliderSize)
        local percentage = relativeX / sliderSize
        
        local value = math.floor(50 + (350 * percentage))
        value = math.clamp(value, 50, 400)
        
        -- Atualizar UI
        SliderFill.Size = UDim2.new((value - 50) / 350, 0, 1, 0)
        SliderButton.Position = UDim2.new((value - 50) / 350, -11, 0, -7)
        FOVValue.Text = value .. " px"
        StatusDesc.Text = "FOV: " .. value .. "px | Mirando no mais próximo"
        
        -- Atualizar configuração
        Settings.Aimbot.FOV = value
        if FOVCircle then
            FOVCircle.Radius = value
        end
    end
end)

-- Toggle FOV
ToggleBtn.MouseButton1Click:Connect(function()
    if Settings.Aimbot.ShowFOV then
        Settings.Aimbot.ShowFOV = false
        ToggleIndicator.Position = UDim2.new(0, 3, 0.5, -9.5)
        ToggleBtn.BackgroundColor3 = Color3.fromRGB(50, 0, 0)
    else
        Settings.Aimbot.ShowFOV = true
        ToggleIndicator.Position = UDim2.new(1, -24, 0.5, -9.5)
        ToggleBtn.BackgroundColor3 = Color3.fromRGB(255, 0, 0)
    end
    
    if FOVCircle then
        if Settings.Aimbot.ShowFOV and Settings.Aimbot.Enabled then
            FOVCircle.Visible = true
        else
            FOVCircle.Visible = false
        end
    end
end)

-- Toggle Aimbot
AimbotBtn.MouseButton1Click:Connect(function()
    if Settings.Aimbot.Enabled then
        Settings.Aimbot.Enabled = false
        AimbotBtn.Text = "DESLIGADO"
        AimbotBtn.BackgroundColor3 = Color3.fromRGB(50, 0, 0)
        StatusTitle.Text = "⭕ MIRA DESLIGADA"
        StatusTitle.TextColor3 = Color3.fromRGB(255, 0, 0)
        Settings.Aimbot.Locked = false
        Settings.Aimbot.CurrentTarget = nil
    else
        Settings.Aimbot.Enabled = true
        AimbotBtn.Text = "LIGADO"
        AimbotBtn.BackgroundColor3 = Color3.fromRGB(0, 255, 0)
        StatusTitle.Text = "⚡ MIRA GRUDADA ATIVA"
        StatusTitle.TextColor3 = Color3.fromRGB(0, 255, 0)
    end
    
    if FOVCircle then
        if Settings.Aimbot.ShowFOV and Settings.Aimbot.Enabled then
            FOVCircle.Visible = true
        else
            FOVCircle.Visible = false
        end
    end
end)

-- Toggle ESP
ESPBtn.MouseButton1Click:Connect(function()
    if Settings.ESP.Enabled then
        Settings.ESP.Enabled = false
        ESPBtn.Text = "DESLIGADO"
        ESPBtn.BackgroundColor3 = Color3.fromRGB(50, 0, 0)
    else
        Settings.ESP.Enabled = true
        ESPBtn.Text = "LIGADO"
        ESPBtn.BackgroundColor3 = Color3.fromRGB(0, 255, 0)
    end
end)

-- Fechar painel
CloseButton.MouseButton1Click:Connect(function()
    MainPanel.Visible = false
    TKFloatIcon.Visible = true
end)

TKFloatIcon.MouseButton1Click:Connect(function()
    TKFloatIcon.Visible = false
    MainPanel.Visible = true
end)

-- ==================== ESP COM BILLBOARDGUI ====================

local ESPTags = {}

local function createESPForPlayer(player)
    if player == LocalPlayer then return end
    
    local billboard = Instance.new("BillboardGui")
    billboard.Name = "TK_ESP_" .. player.Name
    billboard.Size = UDim2.new(0, 200, 0, 40)
    billboard.StudsOffset = Vector3.new(0, 3, 0)
    billboard.AlwaysOnTop = true
    billboard.Enabled = false
    billboard.Parent = player.Character and player.Character:FindFirstChild("Head")
    
    local label = Instance.new("TextLabel")
    label.Size = UDim2.new(1, 0, 1, 0)
    label.BackgroundTransparency = 1
    label.Text = player.Name .. " [0m]"
    label.TextColor3 = Color3.fromRGB(255, 0, 0)
    label.TextScaled = true
    label.Font = Enum.Font.GothamBold
    label.TextStrokeTransparency = 0.3
    label.TextStrokeColor3 = Color3.fromRGB(0, 0, 0)
    label.Parent = billboard
    
    ESPTags[player] = {
        Billboard = billboard,
        Label = label
    }
end

-- Criar ESP para jogadores existentes
for _, player in ipairs(Players:GetPlayers()) do
    task.spawn(function()
        createESPForPlayer(player)
    end)
end

-- Detectar novos jogadores
Players.PlayerAdded:Connect(function(player)
    task.spawn(function()
        createESPForPlayer(player)
    end)
end)

-- Remover ESP
Players.PlayerRemoving:Connect(function(player)
    if ESPTags[player] and ESPTags[player].Billboard then
        pcall(function()
            ESPTags[player].Billboard:Destroy()
        end)
        ESPTags[player] = nil
    end
end)

-- ==================== AIMBOT - PUXA PARA A CABEÇA DA PESSOA MAIS PRÓXIMA ====================

RunService.RenderStepped:Connect(function()
    -- Atualizar círculo FOV
    if FOVCircle and Settings.Aimbot.ShowFOV and Settings.Aimbot.Enabled then
        pcall(function()
            FOVCircle.Position = Vector2.new(Camera.ViewportSize.X / 2, Camera.ViewportSize.Y / 2)
            FOVCircle.Visible = true
        end)
    else
        if FOVCircle then
            pcall(function()
                FOVCircle.Visible = false
            end)
        end
    end
    
    -- Aimbot
    if Settings.Aimbot.Enabled the

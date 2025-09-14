-- Remote Spy - Versão com Monitoramento Direto
-- Para exploits que não suportam hooks

local Players = game:GetService("Players")
local CoreGui = game:GetService("CoreGui")
local UserInputService = game:GetService("UserInputService")
local RunService = game:GetService("RunService")
local ReplicatedStorage = game:GetService("ReplicatedStorage")

-- Remover GUI anterior
if CoreGui:FindFirstChild("RemoteSpyGUI") then
    CoreGui:FindFirstChild("RemoteSpyGUI"):Destroy()
end

-- Funções de compatibilidade
local setclipboard = setclipboard or toclipboard or (syn and syn.write_clipboard) or function(text) 
    print("Clipboard:", text) 
end

-- Dados dos remotes
local monitoredRemotes = {}
local remoteData = {}
local connections = {}

-- Criar GUI
local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Name = "RemoteSpyGUI"
ScreenGui.ResetOnSpawn = false

-- Proteger GUI
if syn and syn.protect_gui then
    syn.protect_gui(ScreenGui)
end
ScreenGui.Parent = CoreGui

-- Frame principal
local MainFrame = Instance.new("Frame")
MainFrame.Parent = ScreenGui
MainFrame.BackgroundColor3 = Color3.fromRGB(35, 35, 35)
MainFrame.BorderSizePixel = 0
MainFrame.Position = UDim2.new(0.1, 0, 0.2, 0)
MainFrame.Size = UDim2.new(0, 500, 0, 400)
MainFrame.Active = true
MainFrame.Draggable = true

local MainCorner = Instance.new("UICorner")
MainCorner.CornerRadius = UDim.new(0, 10)
MainCorner.Parent = MainFrame

-- Header
local Header = Instance.new("Frame")
Header.Parent = MainFrame
Header.BackgroundColor3 = Color3.fromRGB(25, 25, 25)
Header.BorderSizePixel = 0
Header.Size = UDim2.new(1, 0, 0, 40)

local HeaderCorner = Instance.new("UICorner")
HeaderCorner.CornerRadius = UDim.new(0, 10)
HeaderCorner.Parent = Header

local Title = Instance.new("TextLabel")
Title.Parent = Header
Title.BackgroundTransparency = 1
Title.Position = UDim2.new(0, 15, 0, 0)
Title.Size = UDim2.new(1, -100, 1, 0)
Title.Font = Enum.Font.GothamBold
Title.Text = "Remote Spy - Monitor Direto"
Title.TextColor3 = Color3.fromRGB(255, 255, 255)
Title.TextSize = 16
Title.TextXAlignment = Enum.TextXAlignment.Left

-- Botão Fechar
local CloseButton = Instance.new("TextButton")
CloseButton.Parent = Header
CloseButton.BackgroundColor3 = Color3.fromRGB(220, 50, 50)
CloseButton.BorderSizePixel = 0
CloseButton.Position = UDim2.new(1, -35, 0, 5)
CloseButton.Size = UDim2.new(0, 30, 0, 30)
CloseButton.Font = Enum.Font.GothamBold
CloseButton.Text = "X"
CloseButton.TextColor3 = Color3.fromRGB(255, 255, 255)
CloseButton.TextSize = 18

local CloseCorner = Instance.new("UICorner")
CloseCorner.CornerRadius = UDim.new(0, 15)
CloseCorner.Parent = CloseButton

-- Botão Minimizar
local MinimizeButton = Instance.new("TextButton")
MinimizeButton.Parent = Header
MinimizeButton.BackgroundColor3 = Color3.fromRGB(100, 100, 100)
MinimizeButton.BorderSizePixel = 0
MinimizeButton.Position = UDim2.new(1, -70, 0, 5)
MinimizeButton.Size = UDim2.new(0, 30, 0, 30)
MinimizeButton.Font = Enum.Font.GothamBold
MinimizeButton.Text = "-"
MinimizeButton.TextColor3 = Color3.fromRGB(255, 255, 255)
MinimizeButton.TextSize = 20

local MinCorner = Instance.new("UICorner")
MinCorner.CornerRadius = UDim.new(0, 15)
MinCorner.Parent = MinimizeButton

-- Container de conteúdo
local ContentFrame = Instance.new("Frame")
ContentFrame.Parent = MainFrame
ContentFrame.BackgroundTransparency = 1
ContentFrame.Position = UDim2.new(0, 10, 0, 50)
ContentFrame.Size = UDim2.new(1, -20, 1, -60)

-- Lista de remotes encontrados
local RemotesList = Instance.new("ScrollingFrame")
RemotesList.Parent = ContentFrame
RemotesList.BackgroundColor3 = Color3.fromRGB(45, 45, 45)
RemotesList.BorderSizePixel = 0
RemotesList.Position = UDim2.new(0, 0, 0, 0)
RemotesList.Size = UDim2.new(0.6, -5, 1, -40)
RemotesList.ScrollBarThickness = 8
RemotesList.ScrollBarImageColor3 = Color3.fromRGB(100, 100, 100)

local ListCorner = Instance.new("UICorner")
ListCorner.CornerRadius = UDim.new(0, 8)
ListCorner.Parent = RemotesList

local ListLayout = Instance.new("UIListLayout")
ListLayout.Parent = RemotesList
ListLayout.Padding = UDim.new(0, 3)
ListLayout.SortOrder = Enum.SortOrder.Name

-- Painel de informações
local InfoFrame = Instance.new("Frame")
InfoFrame.Parent = ContentFrame
InfoFrame.BackgroundColor3 = Color3.fromRGB(45, 45, 45)
InfoFrame.BorderSizePixel = 0
InfoFrame.Position = UDim2.new(0.6, 5, 0, 0)
InfoFrame.Size = UDim2.new(0.4, -5, 1, -40)

local InfoCorner = Instance.new("UICorner")
InfoCorner.CornerRadius = UDim.new(0, 8)
InfoCorner.Parent = InfoFrame

-- Texto de informações
local InfoText = Instance.new("TextLabel")
InfoText.Parent = InfoFrame
InfoText.BackgroundTransparency = 1
InfoText.Position = UDim2.new(0, 10, 0, 10)
InfoText.Size = UDim2.new(1, -20, 1, -80)
InfoText.Font = Enum.Font.Code
InfoText.Text = "Clique em um remote para ver detalhes"
InfoText.TextColor3 = Color3.fromRGB(200, 200, 200)
InfoText.TextSize = 11
InfoText.TextWrapped = true
InfoText.TextYAlignment = Enum.TextYAlignment.Top

-- Botão de copiar
local CopyButton = Instance.new("TextButton")
CopyButton.Parent = InfoFrame
CopyButton.BackgroundColor3 = Color3.fromRGB(70, 130, 250)
CopyButton.BorderSizePixel = 0
CopyButton.Position = UDim2.new(0, 10, 1, -35)
CopyButton.Size = UDim2.new(1, -20, 0, 25)
CopyButton.Font = Enum.Font.Gotham
CopyButton.Text = "Copiar Caminho"
CopyButton.TextColor3 = Color3.fromRGB(255, 255, 255)
CopyButton.TextSize = 12

local CopyCorner = Instance.new("UICorner")
CopyCorner.CornerRadius = UDim.new(0, 5)
CopyCorner.Parent = CopyButton

-- Botões de controle
local ControlFrame = Instance.new("Frame")
ControlFrame.Parent = ContentFrame
ControlFrame.BackgroundTransparency = 1
ControlFrame.Position = UDim2.new(0, 0, 1, -30)
ControlFrame.Size = UDim2.new(1, 0, 0, 30)

local ScanButton = Instance.new("TextButton")
ScanButton.Parent = ControlFrame
ScanButton.BackgroundColor3 = Color3.fromRGB(50, 200, 50)
ScanButton.BorderSizePixel = 0
ScanButton.Position = UDim2.new(0, 0, 0, 0)
ScanButton.Size = UDim2.new(0, 100, 1, 0)
ScanButton.Font = Enum.Font.Gotham
ScanButton.Text = "Escanear"
ScanButton.TextColor3 = Color3.fromRGB(255, 255, 255)
ScanButton.TextSize = 12

local ScanCorner = Instance.new("UICorner")
ScanCorner.CornerRadius = UDim.new(0, 5)
ScanCorner.Parent = ScanButton

local ClearButton = Instance.new("TextButton")
ClearButton.Parent = ControlFrame
ClearButton.BackgroundColor3 = Color3.fromRGB(200, 50, 50)
ClearButton.BorderSizePixel = 0
ClearButton.Position = UDim2.new(0, 110, 0, 0)
ClearButton.Size = UDim2.new(0, 80, 1, 0)
ClearButton.Font = Enum.Font.Gotham
ClearButton.Text = "Limpar"
ClearButton.TextColor3 = Color3.fromRGB(255, 255, 255)
ClearButton.TextSize = 12

local ClearCorner = Instance.new("UICorner")
ClearCorner.CornerRadius = UDim.new(0, 5)
ClearCorner.Parent = ClearButton

-- Status
local StatusLabel = Instance.new("TextLabel")
StatusLabel.Parent = ControlFrame
StatusLabel.BackgroundTransparency = 1
StatusLabel.Position = UDim2.new(0, 200, 0, 0)
StatusLabel.Size = UDim2.new(1, -200, 1, 0)
StatusLabel.Font = Enum.Font.Gotham
StatusLabel.Text = "Pressione 'Escanear' para encontrar remotes"
StatusLabel.TextColor3 = Color3.fromRGB(150, 150, 150)
StatusLabel.TextSize = 11
StatusLabel.TextXAlignment = Enum.TextXAlignment.Left

-- Variáveis de controle
local selectedRemote = nil
local isMinimized = false
local foundRemotes = {}

-- Função para obter caminho da instância
local function getPath(obj)
    if not obj or not obj.Parent then return "nil" end
    
    local path = {}
    local current = obj
    
    while current and current ~= game do
        local name = current.Name
        if name:match("^[%a_][%w_]*$") and not tonumber(name:sub(1,1)) then
            table.insert(path, 1, name)
        else
            table.insert(path, 1, '["' .. name:gsub('"', '\\"') .. '"]')
        end
        current = current.Parent
    end
    
    if obj.Parent == game then
        return "game." .. table.concat(path, ".")
    else
        local success, service = pcall(function() return game:GetService(obj.Parent.ClassName) end)
        if success and service == obj.Parent then
            return 'game:GetService("' .. obj.Parent.ClassName .. '").' .. table.concat(path, ".", 2)
        end
    end
    
    return "game." .. table.concat(path, ".")
end

-- Função para criar item de remote na lista
local function createRemoteItem(remote)
    local remoteName = remote.Name
    local remoteType = remote.ClassName
    local remotePath = getPath(remote)
    
    local ItemFrame = Instance.new("Frame")
    ItemFrame.Parent = RemotesList
    ItemFrame.BackgroundColor3 = Color3.fromRGB(60, 60, 60)
    ItemFrame.BorderSizePixel = 0
    ItemFrame.Size = UDim2.new(1, -10, 0, 35)
    
    local ItemCorner = Instance.new("UICorner")
    ItemCorner.CornerRadius = UDim.new(0, 5)
    ItemCorner.Parent = ItemFrame
    
    local TypeLabel = Instance.new("TextLabel")
    TypeLabel.Parent = ItemFrame
    TypeLabel.BackgroundTransparency = 1
    TypeLabel.Position = UDim2.new(0, 8, 0, 0)
    TypeLabel.Size = UDim2.new(0, 20, 1, 0)
    TypeLabel.Font = Enum.Font.GothamBold
    TypeLabel.Text = remoteType == "RemoteEvent" and "E" or "F"
    TypeLabel.TextColor3 = remoteType == "RemoteEvent" and Color3.fromRGB(100, 200, 255) or Color3.fromRGB(255, 200, 100)
    TypeLabel.TextSize = 14
    
    local NameLabel = Instance.new("TextLabel")
    NameLabel.Parent = ItemFrame
    NameLabel.BackgroundTransparency = 1
    NameLabel.Position = UDim2.new(0, 35, 0, 0)
    NameLabel.Size = UDim2.new(1, -40, 1, 0)
    NameLabel.Font = Enum.Font.Gotham
    NameLabel.Text = remoteName
    NameLabel.TextColor3 = Color3.fromRGB(255, 255, 255)
    NameLabel.TextSize = 12
    NameLabel.TextXAlignment = Enum.TextXAlignment.Left
    NameLabel.TextTruncate = Enum.TextTruncate.AtEnd
    
    local Button = Instance.new("TextButton")
    Button.Parent = ItemFrame
    Button.BackgroundTransparency = 1
    Button.Size = UDim2.new(1, 0, 1, 0)
    Button.Text = ""
    
    Button.MouseButton1Click:Connect(function()
        selectedRemote = remote
        
        -- Destacar seleção
        for _, child in pairs(RemotesList:GetChildren()) do
            if child:IsA("Frame") then
                child.BackgroundColor3 = Color3.fromRGB(60, 60, 60)
            end
        end
        ItemFrame.BackgroundColor3 = Color3.fromRGB(70, 130, 250)
        
        -- Atualizar informações
        local methodName = remoteType == "RemoteEvent" and "FireServer" or "InvokeServer"
        local info = string.format("Nome: %s\nTipo: %s\nLocalização: %s\n\nCaminho completo:\n%s\n\nPara usar:\n%s:%s(args)",
            remoteName,
            remoteType,
            remote.Parent and remote.Parent.Name or "Desconhecido",
            remotePath,
            remotePath,
            methodName
        )
        
        InfoText.Text = info
    end)
    
    return ItemFrame
end

-- Função para escanear remotes
local function scanForRemotes()
    -- Limpar lista atual
    for _, child in pairs(RemotesList:GetChildren()) do
        if child:IsA("Frame") then
            child:Destroy()
        end
    end
    
    foundRemotes = {}
    local count = 0
    
    -- Escanear no jogo inteiro
    local function scanDescendants(parent)
        for _, obj in pairs(parent:GetDescendants()) do
            if obj:IsA("RemoteEvent") or obj:IsA("RemoteFunction") then
                if not foundRemotes[obj] then
                    foundRemotes[obj] = true
                    createRemoteItem(obj)
                    count = count + 1
                end
            end
        end
    end
    
    -- Escanear locais importantes
    scanDescendants(game.ReplicatedStorage)
    scanDescendants(game.ReplicatedFirst)
    scanDescendants(game.Lighting)
    scanDescendants(game.ServerStorage or game.ReplicatedStorage) -- Fallback
    scanDescendants(game.StarterPlayer)
    scanDescendants(game.StarterPack)
    scanDescendants(game.StarterGui)
    
    -- Atualizar interface
    RemotesList.CanvasSize = UDim2.new(0, 0, 0, ListLayout.AbsoluteContentSize.Y + 10)
    StatusLabel.Text = string.format("Encontrados: %d remotes", count)
end

-- Eventos dos botões
CloseButton.MouseButton1Click:Connect(function()
    ScreenGui:Destroy()
end)

MinimizeButton.MouseButton1Click:Connect(function()
    isMinimized = not isMinimized
    if isMinimized then
        MainFrame:TweenSize(UDim2.new(0, 500, 0, 40), "Out", "Quad", 0.3)
        ContentFrame.Visible = false
        MinimizeButton.Text = "+"
    else
        MainFrame:TweenSize(UDim2.new(0, 500, 0, 400), "Out", "Quad", 0.3)
        ContentFrame.Visible = true
        MinimizeButton.Text = "-"
    end
end)

ScanButton.MouseButton1Click:Connect(function()
    ScanButton.Text = "Escaneando..."
    ScanButton.BackgroundColor3 = Color3.fromRGB(255, 165, 0)
    
    wait(0.1) -- Para mostrar o feedback visual
    scanForRemotes()
    
    ScanButton.Text = "Escanear"
    ScanButton.BackgroundColor3 = Color3.fromRGB(50, 200, 50)
end)

ClearButton.MouseButton1Click:Connect(function()
    for _, child in pairs(RemotesList:GetChildren()) do
        if child:IsA("Frame") then
            child:Destroy()
        end
    end
    foundRemotes = {}
    selectedRemote = nil
    InfoText.Text = "Clique em um remote para ver detalhes"
    StatusLabel.Text = "Lista limpa"
end)

CopyButton.MouseButton1Click:Connect(function()
    if selectedRemote then
        local path = getPath(selectedRemote)
        local methodName = selectedRemote:IsA("RemoteEvent") and "FireServer" or "InvokeServer"
        local fullCode = path .. ":" .. methodName .. "()"
        
        setclipboard(fullCode)
        
        CopyButton.Text = "Copiado!"
        CopyButton.BackgroundColor3 = Color3.fromRGB(50, 200, 50)
        
        wait(1)
        
        CopyButton.Text = "Copiar Caminho"
        CopyButton.BackgroundColor3 = Color3.fromRGB(70, 130, 250)
    end
end)

-- Hotkey P para toggle
UserInputService.InputBegan:Connect(function(input, gameProcessed)
    if not gameProcessed and input.KeyCode == Enum.KeyCode.P then
        ScreenGui.Enabled = not ScreenGui.Enabled
    end
end)

-- Auto-scan inicial
wait(1)
scanForRemotes()

print("Remote Spy carregado!")
print("Pressione P para mostrar/ocultar")
print("Use o botão 'Escanear' para encontrar novos remotes")

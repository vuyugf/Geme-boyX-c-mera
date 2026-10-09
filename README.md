-- Services
local HttpService = game:GetService("HttpService")
local RunService = game:GetService("RunService")
local CoreGui = game:GetService("CoreGui")
local Players = game:GetService("Players")

local LocalPlayer = Players.LocalPlayer
local Camera = workspace.CurrentCamera
local SAVE_FILE = "GameBoyX_Studio_Save.json"

-- Destruir GUI anterior se existir
if CoreGui:FindFirstChild("GameBoyXCameraGui") then
    CoreGui.GameBoyXCameraGui:Destroy()
end

-- ScreenGui Principal
local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Name = "GameBoyXCameraGui"
ScreenGui.ResetOnSpawn = false

if syn and syn.protect_gui then
    syn.protect_gui(ScreenGui)
    ScreenGui.Parent = CoreGui
elseif gethui then
    ScreenGui.Parent = gethui()
else
    ScreenGui.Parent = CoreGui
end

----------------------------------------------------
-- BOTÃO FLUTUANTE (BOLINHA COM BORDA VERDE)
----------------------------------------------------
local ToggleButton = Instance.new("TextButton")
local UICornerBtn = Instance.new("UICorner")
local UIStrokeBtn = Instance.new("UIStroke")

ToggleButton.Name = "ToggleButton"
ToggleButton.Size = UDim2.new(0, 65, 0, 65)
ToggleButton.Position = UDim2.new(0.05, 0, 0.4, 0)
ToggleButton.BackgroundColor3 = Color3.fromRGB(15, 15, 15)
ToggleButton.Active = true
ToggleButton.Draggable = true
ToggleButton.Text = "Câmera"
ToggleButton.TextColor3 = Color3.fromRGB(85, 255, 0)
ToggleButton.Font = Enum.Font.SourceSansBold
ToggleButton.TextSize = 15
ToggleButton.Parent = ScreenGui

UICornerBtn.CornerRadius = UDim.new(1, 0)
UICornerBtn.Parent = ToggleButton

UIStrokeBtn.Color = Color3.fromRGB(85, 255, 0)
UIStrokeBtn.Thickness = 3
UIStrokeBtn.Parent = ToggleButton

----------------------------------------------------
-- PAINEL PRINCIPAL GORDINHO
----------------------------------------------------
local MainFrame = Instance.new("Frame")
local UICornerMain = Instance.new("UICorner")
local UIStrokeMain = Instance.new("UIStroke")
local Title = Instance.new("TextLabel")
local CloseButton = Instance.new("TextButton")

MainFrame.Name = "MainFrame"
MainFrame.Size = UDim2.new(0, 420, 0, 380)
MainFrame.Position = UDim2.new(0.5, -210, 0.5, -190)
MainFrame.BackgroundColor3 = Color3.fromRGB(18, 18, 18)
MainFrame.Visible = true
MainFrame.Active = true
MainFrame.Draggable = true
MainFrame.Parent = ScreenGui

UICornerMain.CornerRadius = UDim.new(0, 16)
UICornerMain.Parent = MainFrame

UIStrokeMain.Color = Color3.fromRGB(85, 255, 0)
UIStrokeMain.Thickness = 2
UIStrokeMain.Parent = MainFrame

Title.Name = "Title"
Title.Size = UDim2.new(1, -50, 0, 45)
Title.Position = UDim2.new(0, 20, 0, 0)
Title.BackgroundTransparency = 1
Title.Text = "GameBoy<font color=\"rgb(85,255,0)\">X Studio</font>"
Title.RichText = true
Title.TextColor3 = Color3.fromRGB(255, 255, 255)
Title.TextSize = 22
Title.Font = Enum.Font.SourceSansBold
Title.TextXAlignment = Enum.TextXAlignment.Left
Title.Parent = MainFrame

CloseButton.Name = "CloseButton"
CloseButton.Size = UDim2.new(0, 30, 0, 30)
CloseButton.Position = UDim2.new(1, -40, 0, 10)
CloseButton.BackgroundTransparency = 1
CloseButton.Text = "X"
CloseButton.TextColor3 = Color3.fromRGB(255, 85, 85)
CloseButton.TextSize = 20
CloseButton.Font = Enum.Font.SourceSansBold
CloseButton.Parent = MainFrame

----------------------------------------------------
-- CONTEÚDO PRINCIPAL
----------------------------------------------------
local ContentFrame = Instance.new("Frame")
ContentFrame.Size = UDim2.new(1, -30, 1, -65)
ContentFrame.Position = UDim2.new(0, 15, 0, 50)
ContentFrame.BackgroundColor3 = Color3.fromRGB(10, 10, 10)
ContentFrame.Parent = MainFrame
Instance.new("UICorner", ContentFrame).CornerRadius = UDim.new(0, 12)

local TakePhotoBtn = Instance.new("TextButton")
TakePhotoBtn.Size = UDim2.new(0.48, -5, 0, 40)
TakePhotoBtn.Position = UDim2.new(0, 10, 0, 10)
TakePhotoBtn.BackgroundColor3 = Color3.fromRGB(85, 255, 0)
TakePhotoBtn.Text = "📷 Foto (3s)"
TakePhotoBtn.TextColor3 = Color3.fromRGB(0, 0, 0)
TakePhotoBtn.Font = Enum.Font.SourceSansBold
TakePhotoBtn.TextSize = 15
TakePhotoBtn.Parent = ContentFrame
Instance.new("UICorner", TakePhotoBtn).CornerRadius = UDim.new(0, 8)

local RecordBtn = Instance.new("TextButton")
RecordBtn.Size = UDim2.new(0.48, -5, 0, 40)
RecordBtn.Position = UDim2.new(0.52, 0, 0, 10)
RecordBtn.BackgroundColor3 = Color3.fromRGB(255, 170, 0)
RecordBtn.Text = "🎥 Gravação (9s)"
RecordBtn.TextColor3 = Color3.fromRGB(0, 0, 0)
RecordBtn.Font = Enum.Font.SourceSansBold
RecordBtn.TextSize = 15
RecordBtn.Parent = ContentFrame
Instance.new("UICorner", RecordBtn).CornerRadius = UDim.new(0, 8)

local ScrollMedia = Instance.new("ScrollingFrame")
ScrollMedia.Size = UDim2.new(1, -20, 1, -65)
ScrollMedia.Position = UDim2.new(0, 10, 0, 58)
ScrollMedia.BackgroundTransparency = 1
ScrollMedia.CanvasSize = UDim2.new(0, 0, 0, 0)
ScrollMedia.ScrollBarThickness = 4
ScrollMedia.Parent = ContentFrame

local LayoutMedia = Instance.new("UIListLayout")
LayoutMedia.Parent = ScrollMedia
LayoutMedia.Padding = UDim.new(0, 6)

----------------------------------------------------
-- POPUP DE NOME
----------------------------------------------------
local NameModal = Instance.new("Frame")
NameModal.Size = UDim2.new(0, 270, 0, 140)
NameModal.Position = UDim2.new(0.5, -135, 0.5, -70)
NameModal.BackgroundColor3 = Color3.fromRGB(25, 25, 25)
NameModal.Visible = false
NameModal.ZIndex = 20
NameModal.Parent = ScreenGui
Instance.new("UICorner", NameModal).CornerRadius = UDim.new(0, 10)

local ModalTitle = Instance.new("TextLabel")
ModalTitle.Size = UDim2.new(1, 0, 0, 30)
ModalTitle.BackgroundTransparency = 1
ModalTitle.Text = "Digite o Nome:"
ModalTitle.TextColor3 = Color3.fromRGB(255, 255, 255)
ModalTitle.Font = Enum.Font.SourceSansBold
ModalTitle.TextSize = 16
ModalTitle.ZIndex = 21
ModalTitle.Parent = NameModal

local MediaNameInput = Instance.new("TextBox")
MediaNameInput.Size = UDim2.new(1, -30, 0, 32)
MediaNameInput.Position = UDim2.new(0, 15, 0, 45)
MediaNameInput.BackgroundColor3 = Color3.fromRGB(15, 15, 15)
MediaNameInput.TextColor3 = Color3.fromRGB(85, 255, 0)
MediaNameInput.PlaceholderText = "Nome..."
MediaNameInput.Font = Enum.Font.SourceSans
MediaNameInput.TextSize = 14
MediaNameInput.ZIndex = 21
MediaNameInput.Parent = NameModal
Instance.new("UICorner", MediaNameInput).CornerRadius = UDim.new(0, 6)

local ConfirmNameBtn = Instance.new("TextButton")
ConfirmNameBtn.Size = UDim2.new(1, -30, 0, 32)
ConfirmNameBtn.Position = UDim2.new(0, 15, 0, 90)
ConfirmNameBtn.BackgroundColor3 = Color3.fromRGB(85, 255, 0)
ConfirmNameBtn.Text = "Confirmar"
ConfirmNameBtn.TextColor3 = Color3.fromRGB(0, 0, 0)
ConfirmNameBtn.Font = Enum.Font.SourceSansBold
ConfirmNameBtn.TextSize = 15
ConfirmNameBtn.ZIndex = 21
ConfirmNameBtn.Parent = NameModal
Instance.new("UICorner", ConfirmNameBtn).CornerRadius = UDim.new(0, 6)

----------------------------------------------------
-- ABA / JANELA DE EXIBIÇÃO DA FOTO E GRAVAÇÃO
----------------------------------------------------
local MediaViewerFrame = Instance.new("Frame")
MediaViewerFrame.Name = "MediaViewerFrame"
MediaViewerFrame.Size = UDim2.new(0, 360, 0, 320)
MediaViewerFrame.Position = UDim2.new(0.5, -180, 0.5, -160)
MediaViewerFrame.BackgroundColor3 = Color3.fromRGB(20, 20, 20)
MediaViewerFrame.Visible = false
MediaViewerFrame.ZIndex = 30
MediaViewerFrame.Active = true
MediaViewerFrame.Draggable = true
MediaViewerFrame.Parent = ScreenGui

Instance.new("UICorner", MediaViewerFrame).CornerRadius = UDim.new(0, 12)
local ViewerStroke = Instance.new("UIStroke")
ViewerStroke.Color = Color3.fromRGB(85, 255, 0)
ViewerStroke.Thickness = 2
ViewerStroke.Parent = MediaViewerFrame

local ViewerTitle = Instance.new("TextLabel")
ViewerTitle.Size = UDim2.new(1, -110, 0, 35)
ViewerTitle.Position = UDim2.new(0, 12, 0, 0)
ViewerTitle.BackgroundTransparency = 1
ViewerTitle.Text = "Exibindo Mídia"
ViewerTitle.TextColor3 = Color3.fromRGB(255, 255, 255)
ViewerTitle.Font = Enum.Font.SourceSansBold
ViewerTitle.TextSize = 16
ViewerTitle.TextXAlignment = Enum.TextXAlignment.Left
ViewerTitle.ZIndex = 31
ViewerTitle.Parent = MediaViewerFrame

-- Botão de Salvar (💾)
local SaveMediaBtn = Instance.new("TextButton")
SaveMediaBtn.Size = UDim2.new(0, 32, 0, 32)
SaveMediaBtn.Position = UDim2.new(1, -75, 0, 3)
SaveMediaBtn.BackgroundTransparency = 1
SaveMediaBtn.Text = "💾"
SaveMediaBtn.TextColor3 = Color3.fromRGB(85, 255, 0)
SaveMediaBtn.Font = Enum.Font.SourceSansBold
SaveMediaBtn.TextSize = 18
SaveMediaBtn.ZIndex = 31
SaveMediaBtn.Parent = MediaViewerFrame

-- Botão de Fechar (X)
local CloseViewerBtn = Instance.new("TextButton")
CloseViewerBtn.Size = UDim2.new(0, 32, 0, 32)
CloseViewerBtn.Position = UDim2.new(1, -38, 0, 3)
CloseViewerBtn.BackgroundTransparency = 1
CloseViewerBtn.Text = "X"
CloseViewerBtn.TextColor3 = Color3.fromRGB(255, 85, 85)
CloseViewerBtn.Font = Enum.Font.SourceSansBold
CloseViewerBtn.TextSize = 20
CloseViewerBtn.ZIndex = 31
CloseViewerBtn.Parent = MediaViewerFrame

-- VIEWPORTFRAME (QUADRADINHO)
local ViewportDisplay = Instance.new("ViewportFrame")
ViewportDisplay.Size = UDim2.new(1, -20, 1, -50)
ViewportDisplay.Position = UDim2.new(0, 10, 0, 40)
ViewportDisplay.BackgroundColor3 = Color3.fromRGB(30, 30, 35)
ViewportDisplay.LightColor = Color3.fromRGB(255, 255, 255)
ViewportDisplay.LightDirection = Vector3.new(-1, -1, -1)
ViewportDisplay.Ambient = Color3.fromRGB(200, 200, 200)
ViewportDisplay.ZIndex = 31
ViewportDisplay.Parent = MediaViewerFrame
Instance.new("UICorner", ViewportDisplay).CornerRadius = UDim.new(0, 8)

----------------------------------------------------
-- LÓGICA DE MONTAGEM E CLONAGEM DO PERSONAGEM
----------------------------------------------------
local savedPhotos = {}
local savedRecordings = {}

local currentMode = ""
local tempCamCFrame = nil
local tempVideoFrames = {}
local isRecording = false
local isVideoPlaying = false

-- Clona o personagem garantindo visibilidade e ancoragem
local function cloneCharacterCorrectly(char)
    if not char then return nil end
    char.Archivable = true
    local clone = char:Clone()
    
    for _, part in ipairs(clone:GetDescendants()) do
        if part:IsA("BasePart") then
            part.Anchored = true
            part.CanCollide = false
            part.LocalTransparencyModifier = 0 -- Força a ficar visível mesmo em 1ª pessoa
            part.Transparency = 0
        elseif part:IsA("Script") or part:IsA("LocalScript") then
            part:Destroy()
        end
    end
    
    return clone
end

-- Monta o mundo e o jogador no ViewportFrame
local function buildViewportScene(vFrame, targetCF)
    vFrame:ClearAllChildren()

    local vCam = Instance.new("Camera")
    vCam.CFrame = targetCF
    vFrame.CurrentCamera = vCam
    vCam.Parent = vFrame

    local charClones = {}

    -- Clona todos os jogadores (incluindo você)
    for _, plr in ipairs(Players:GetPlayers()) do
        if plr.Character then
            pcall(function()
                local cClone = cloneCharacterCorrectly(plr.Character)
                if cClone then
                    cClone.Parent = vFrame
                    charClones[plr.Name] = cClone
                end
            end)
        end
    end

    -- Clona os blocos/cenário próximos
    for _, item in ipairs(workspace:GetChildren()) do
        if item:IsA("BasePart") or item:IsA("Model") or item:IsA("Folder") then
            if not Players:GetPlayerFromCharacter(item) and item.Name ~= "Camera" then
                pcall(function()
                    item.Archivable = true
                    local itemClone = item:Clone()
                    for _, p in ipairs(itemClone:GetDescendants()) do
                        if p:IsA("BasePart") then
                            p.Anchored = true
                            p.CanCollide = false
                        elseif p:IsA("Script") or p:IsA("LocalScript") then
                            p:Destroy()
                        end
                    end
                    itemClone.Parent = vFrame
                end)
            end
        end
    end

    return vCam, charClones
end

-- Adicionar Foto
local function addPhotoCard(photoName, camCFrame)
    table.insert(savedPhotos, {name = photoName, cf = {camCFrame:GetComponents()}})

    local ItemRow = Instance.new("Frame")
    ItemRow.Size = UDim2.new(1, -5, 0, 35)
    ItemRow.BackgroundTransparency = 1
    ItemRow.Parent = ScrollMedia

    local PhotoBtn = Instance.new("TextButton")
    PhotoBtn.Size = UDim2.new(1, -40, 1, 0)
    PhotoBtn.BackgroundColor3 = Color3.fromRGB(28, 28, 28)
    PhotoBtn.Text = "  🖼️ " .. photoName
    PhotoBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
    PhotoBtn.Font = Enum.Font.SourceSansBold
    PhotoBtn.TextSize = 14
    PhotoBtn.TextXAlignment = Enum.TextXAlignment.Left
    PhotoBtn.Parent = ItemRow
    Instance.new("UICorner", PhotoBtn).CornerRadius = UDim.new(0, 6)

    local DeleteBtn = Instance.new("TextButton")
    DeleteBtn.Size = UDim2.new(0, 32, 0, 35)
    DeleteBtn.Position = UDim2.new(1, -32, 0, 0)
    DeleteBtn.BackgroundColor3 = Color3.fromRGB(40, 20, 20)
    DeleteBtn.Text = "🗑"
    DeleteBtn.TextColor3 = Color3.fromRGB(255, 85, 85)
    DeleteBtn.Font = Enum.Font.SourceSansBold
    DeleteBtn.Parent = ItemRow
    Instance.new("UICorner", DeleteBtn).CornerRadius = UDim.new(0, 6)

    PhotoBtn.MouseButton1Click:Connect(function()
        ViewerTitle.Text = "Foto: " .. photoName
        buildViewportScene(ViewportDisplay, camCFrame)
        MediaViewerFrame.Visible = true
    end)

    DeleteBtn.MouseButton1Click:Connect(function()
        for i, p in ipairs(savedPhotos) do
            if p.name == photoName then table.remove(savedPhotos, i) break end
        end
        ItemRow:Destroy()
        ScrollMedia.CanvasSize = UDim2.new(0, 0, 0, #ScrollMedia:GetChildren() * 41)
    end)

    ScrollMedia.CanvasSize = UDim2.new(0, 0, 0, #ScrollMedia:GetChildren() * 41)
end

-- Adicionar Gravação
local function addVideoCard(videoName, framesData)
    table.insert(savedRecordings, {name = videoName, data = framesData})

    local ItemRow = Instance.new("Frame")
    ItemRow.Size = UDim2.new(1, -5, 0, 35)
    ItemRow.BackgroundTransparency = 1
    ItemRow.Parent = ScrollMedia

    local VideoBtn = Instance.new("TextButton")
    VideoBtn.Size = UDim2.new(1, -40, 1, 0)
    VideoBtn.BackgroundColor3 = Color3.fromRGB(28, 28, 28)
    VideoBtn.Text = "  🎥 " .. videoName .. " (9s)"
    VideoBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
    VideoBtn.Font = Enum.Font.SourceSansBold
    VideoBtn.TextSize = 14
    VideoBtn.TextXAlignment = Enum.TextXAlignment.Left
    VideoBtn.Parent = ItemRow
    Instance.new("UICorner", VideoBtn).CornerRadius = UDim.new(0, 6)

    local DeleteBtn = Instance.new("TextButton")
    DeleteBtn.Size = UDim2.new(0, 32, 0, 35)
    DeleteBtn.Position = UDim2.new(1, -32, 0, 0)
    DeleteBtn.BackgroundColor3 = Color3.fromRGB(40, 20, 20)
    DeleteBtn.Text = "🗑"
    DeleteBtn.TextColor3 = Color3.fromRGB(255, 85, 85)
    DeleteBtn.Font = Enum.Font.SourceSansBold
    DeleteBtn.Parent = ItemRow
    Instance.new("UICorner", DeleteBtn).CornerRadius = UDim.new(0, 6)

    VideoBtn.MouseButton1Click:Connect(function()
        if isVideoPlaying then return end
        isVideoPlaying = true

        ViewerTitle.Text = "▶ Vídeo: " .. videoName
        MediaViewerFrame.Visible = true

        local firstFrame = framesData[1]
        local vCam, charClones = buildViewportScene(ViewportDisplay, firstFrame.camCF)

        task.spawn(function()
            for _, frame in ipairs(framesData) do
                if not MediaViewerFrame.Visible then break end
                vCam.CFrame = frame.camCF
                
                local myClone = charClones[LocalPlayer.Name]
                if myClone and myClone:FindFirstChild("HumanoidRootPart") and frame.charCF then
                    myClone.HumanoidRootPart.CFrame = frame.charCF
                end
                
                task.wait(frame.dt)
            end
            isVideoPlaying = false
        end)
    end)

    DeleteBtn.MouseButton1Click:Connect(function()
        for i, v in ipairs(savedRecordings) do
            if v.name == videoName then table.remove(savedRecordings, i) break end
        end
        ItemRow:Destroy()
        ScrollMedia.CanvasSize = UDim2.new(0, 0, 0, #ScrollMedia:GetChildren() * 41)
    end)

    ScrollMedia.CanvasSize = UDim2.new(0, 0, 0, #ScrollMedia:GetChildren() * 41)
end

----------------------------------------------------
-- BOTÕES DE CAPTURA
----------------------------------------------------
TakePhotoBtn.MouseButton1Click:Connect(function()
    if #savedPhotos >= 5 then
        TakePhotoBtn.Text = "❌ Máx 5 Fotos!"
        task.wait(1.5)
        TakePhotoBtn.Text = "📷 Foto (3s)"
        return
    end

    TakePhotoBtn.BackgroundColor3 = Color3.fromRGB(255, 200, 0)
    for i = 3, 1, -1 do
        TakePhotoBtn.Text = "⏱️ " .. i .. "s..."
        task.wait(1)
    end

    TakePhotoBtn.Text = "📸 Tirada!"
    TakePhotoBtn.BackgroundColor3 = Color3.fromRGB(85, 255, 0)
    tempCamCFrame = Camera.CFrame

    task.wait(0.5)
    currentMode = "Photo"
    MediaNameInput.Text = ""
    NameModal.Visible = true
    TakePhotoBtn.Text = "📷 Foto (3s)"
end)

RecordBtn.MouseButton1Click:Connect(function()
    if #savedRecordings >= 3 then
        RecordBtn.Text = "❌ Máx 3 Gravações!"
        task.wait(1.5)
        RecordBtn.Text = "🎥 Gravação (9s)"
        return
    end

    if isRecording then return end

    local char = LocalPlayer.Character
    local hrp = char and char:FindFirstChild("HumanoidRootPart")

    isRecording = true
    tempVideoFrames = {}
    local startTime = tick()
    local conn

    conn = RunService.RenderStepped:Connect(function(dt)
        local elapsed = tick() - startTime
        if elapsed >= 9 then
            conn:Disconnect()
            isRecording = false
            RecordBtn.Text = "🎥 Gravação (9s)"
            RecordBtn.BackgroundColor3 = Color3.fromRGB(255, 170, 0)

            currentMode = "Video"
            MediaNameInput.Text = ""
            NameModal.Visible = true
        else
            RecordBtn.Text = string.format("🔴 Gravando... %.1fs", 9 - elapsed)
            RecordBtn.BackgroundColor3 = Color3.fromRGB(255, 50, 50)
            
            table.insert(tempVideoFrames, {
                dt = dt,
                camCF = Camera.CFrame,
                charCF = hrp and hrp.CFrame or nil
            })
        end
    end)
end)

ConfirmNameBtn.MouseButton1Click:Connect(function()
    local name = MediaNameInput.Text
    if name == "" then name = "Mídia " .. (#savedPhotos + #savedRecordings + 1) end

    if currentMode == "Photo" and tempCamCFrame then
        addPhotoCard(name, tempCamCFrame)
    elseif currentMode == "Video" and #tempVideoFrames > 0 then
        addVideoCard(name, tempVideoFrames)
    end

    NameModal.Visible = false
end)

SaveMediaBtn.MouseButton1Click:Connect(function()
    if writefile then
        pcall(function()
            writefile(SAVE_FILE, HttpService:JSONEncode({
                photos = savedPhotos,
                recordings = savedRecordings
            }))
        end)
        SaveMediaBtn.Text = "✅"
        task.wait(1)
        SaveMediaBtn.Text = "💾"
    end
end)

CloseViewerBtn.MouseButton1Click:Connect(function()
    MediaViewerFrame.Visible = false
    isVideoPlaying = false
end)

ToggleButton.MouseButton1Click:Connect(function()
    MainFrame.Visible = not MainFrame.Visible
end)

CloseButton.MouseButton1Click:Connect(function()
    MainFrame.Visible = false
end)

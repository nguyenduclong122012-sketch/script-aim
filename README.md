local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local Workspace = game:GetService("Workspace")
local CoreGui = game:GetService("CoreGui")
local UserInputService = game:GetService("UserInputService")

local LocalPlayer = Players.LocalPlayer
local Camera = Workspace.CurrentCamera

local NazilGui = Instance.new("ScreenGui")
NazilGui.Name = "NazilMenuGui_V2"
NazilGui.ResetOnSpawn = false
NazilGui.Parent = CoreGui or LocalPlayer:WaitForChild("PlayerGui")

local Config = {
    -- Color Settings
    MenuColor = Color3.fromRGB(20, 20, 25),
    AccentColor = Color3.fromRGB(0, 170, 255),
    ESPColor = Color3.fromRGB(255, 0, 100),
    
    -- Feature Flags
    ESP_Box = false,
    ESP_Skeleton = false,
    ESP_RainbowTracer = false,
    ESP_CustomTracer = false,
    
    AimHead = false,
    MagicBullet = false,
    NoSpread = false,
    
    ShowFOV = true,
    FOVSize = 100,
    FOVOffsetX = 0,
    FOVOffsetY = 0,
    
    RainbowHue = 0
}

-- FOV Circle Visual
local FOVCircle = Instance.new("Frame")
local FOVCorner = Instance.new("UICorner")
local FOVStroke = Instance.new("UIStroke")

FOVCircle.Name = "FOVCircle"
FOVCircle.Parent = NazilGui
FOVCircle.AnchorPoint = Vector2.new(0.5, 0.5)
FOVCircle.BackgroundTransparency = 1
FOVCircle.Position = UDim2.new(0.5, Config.FOVOffsetX, 0.5, Config.FOVOffsetY)
FOVCircle.Size = UDim2.new(0, Config.FOVSize * 2, 0, Config.FOVSize * 2)

FOVCorner.CornerRadius = UDim.new(1, 0)
FOVCorner.Parent = FOVCircle

FOVStroke.Parent = FOVCircle
FOVStroke.Color = Config.AccentColor
FOVStroke.Thickness = 1.5

-- Toggle Icon Button
local ToggleBtn = Instance.new("ImageButton")
local ToggleCorner = Instance.new("UICorner")
local ToggleStroke = Instance.new("UIStroke")

ToggleBtn.Name = "NazilToggle"
ToggleBtn.Parent = NazilGui
ToggleBtn.Position = UDim2.new(0.02, 0, 0.2, 0)
ToggleBtn.Size = UDim2.new(0, 50, 0, 50)
ToggleBtn.Image = "rbxthumb://type=AvatarHeadShot&id=" .. LocalPlayer.UserId .. "&w=150&h=150"
ToggleBtn.Active = true
ToggleBtn.Draggable = true

ToggleCorner.CornerRadius = UDim.new(1, 0)
ToggleCorner.Parent = ToggleBtn

ToggleStroke.Parent = ToggleBtn
ToggleStroke.Color = Config.AccentColor
ToggleStroke.Thickness = 2

-- Main Frame UI
local MainFrame = Instance.new("Frame")
local MainCorner = Instance.new("UICorner")
local MainStroke = Instance.new("UIStroke")
local Header = Instance.new("Frame")
local TitleLabel = Instance.new("TextLabel")

MainFrame.Name = "MainFrame"
MainFrame.Parent = NazilGui
MainFrame.AnchorPoint = Vector2.new(0.5, 0.5)
MainFrame.Position = UDim2.new(0.5, 0, 0.5, 0)
MainFrame.Size = UDim2.new(0, 460, 0, 350)
MainFrame.BackgroundColor3 = Config.MenuColor
MainFrame.Active = true
MainFrame.Draggable = true

MainCorner.CornerRadius = UDim.new(0, 8)
MainCorner.Parent = MainFrame

MainStroke.Parent = MainFrame
MainStroke.Color = Config.AccentColor
MainStroke.Thickness = 2

Header.Name = "Header"
Header.Parent = MainFrame
Header.Size = UDim2.new(1, 0, 0, 40)
Header.BackgroundTransparency = 1

TitleLabel.Parent = Header
TitleLabel.Position = UDim2.new(0, 15, 0, 8)
TitleLabel.Size = UDim2.new(1, -30, 0, 24)
TitleLabel.Text = "NAZIL MENU"
TitleLabel.TextColor3 = Config.AccentColor
TitleLabel.TextSize = 16
TitleLabel.Font = Enum.Font.SourceSansBold
TitleLabel.TextXAlignment = Enum.TextXAlignment.Left

ToggleBtn.MouseButton1Click:Connect(function()
    MainFrame.Visible = not MainFrame.Visible
end)

-- Navigation Tabs
local TabBar = Instance.new("Frame")
TabBar.Parent = MainFrame
TabBar.Position = UDim2.new(0, 10, 0, 40)
TabBar.Size = UDim2.new(1, -20, 0, 30)
TabBar.BackgroundTransparency = 1

local TabListLayout = Instance.new("UIListLayout")
TabListLayout.Parent = TabBar
TabListLayout.FillDirection = Enum.FillDirection.Horizontal
TabListLayout.SortOrder = Enum.SortOrder.LayoutOrder
TabListLayout.Padding = UDim.new(0, 5)

local ContentContainer = Instance.new("Frame")
ContentContainer.Parent = MainFrame
ContentContainer.Position = UDim2.new(0, 10, 0, 75)
ContentContainer.Size = UDim2.new(1, -20, 1, -85)
ContentContainer.BackgroundTransparency = 1

local Tabs = {}
local TabButtons = {}

local function CreateTab(tabName)
    local TabBtn = Instance.new("TextButton")
    local Corner = Instance.new("UICorner")
    
    TabBtn.Size = UDim2.new(0.23, 0, 1, 0)
    TabBtn.BackgroundColor3 = Color3.fromRGB(35, 35, 40)
    TabBtn.Text = tabName
    TabBtn.TextColor3 = Color3.fromRGB(180, 180, 180)
    TabBtn.Font = Enum.Font.SourceSansBold
    TabBtn.TextSize = 12
    TabBtn.Parent = TabBar
    
    Corner.CornerRadius = UDim.new(0, 6)
    Corner.Parent = TabBtn

    local Scroll = Instance.new("ScrollingFrame")
    Scroll.Size = UDim2.new(1, 0, 1, 0)
    Scroll.BackgroundTransparency = 1
    Scroll.ScrollBarThickness = 3
    Scroll.Visible = false
    Scroll.CanvasSize = UDim2.new(0, 0, 2, 0)
    Scroll.Parent = ContentContainer

    local List = Instance.new("UIListLayout")
    List.Parent = Scroll
    List.SortOrder = Enum.SortOrder.LayoutOrder
    List.Padding = UDim.new(0, 6)

    Tabs[tabName] = Scroll
    TabButtons[tabName] = TabBtn

    TabBtn.MouseButton1Click:Connect(function()
        for name, page in pairs(Tabs) do
            page.Visible = (name == tabName)
            TabButtons[name].BackgroundColor3 = (name == tabName) and Config.AccentColor or Color3.fromRGB(35, 35, 40)
            TabButtons[name].TextColor3 = (name == tabName) and Color3.fromRGB(255, 255, 255) or Color3.fromRGB(180, 180, 180)
        end
    end)

    return Scroll
end

local TabESP = CreateTab("ESP")
local TabABDYEN = CreateTab("ABDYEN")
local TabColor = CreateTab("COLOR")
local TabKhac = CreateTab("KHÁC")

Tabs["ESP"].Visible = true
TabButtons["ESP"].BackgroundColor3 = Config.AccentColor
TabButtons["ESP"].TextColor3 = Color3.fromRGB(255, 255, 255)

-- Component Helpers
local function CreateToggleOption(parentTab, name, defaultState, callback)
    local Container = Instance.new("Frame")
    local Corner = Instance.new("UICorner")
    local Label = Instance.new("TextLabel")
    local SwitchBtn = Instance.new("TextButton")
    local SwitchCorner = Instance.new("UICorner")
    local SwitchCircle = Instance.new("Frame")
    local CircleCorner = Instance.new("UICorner")

    Container.Size = UDim2.new(1, -5, 0, 36)
    Container.BackgroundColor3 = Color3.fromRGB(35, 35, 40)
    Container.Parent = parentTab

    Corner.CornerRadius = UDim.new(0, 6)
    Corner.Parent = Container

    Label.Parent = Container
    Label.Position = UDim2.new(0, 10, 0, 0)
    Label.Size = UDim2.new(0.7, 0, 1, 0)
    Label.Text = name
    Label.TextColor3 = Color3.fromRGB(240, 240, 240)
    Label.TextSize = 13
    Label.Font = Enum.Font.SourceSans
    Label.TextXAlignment = Enum.TextXAlignment.Left

    SwitchBtn.Parent = Container
    SwitchBtn.Position = UDim2.new(1, -45, 0.5, -10)
    SwitchBtn.Size = UDim2.new(0, 38, 0, 20)
    SwitchBtn.BackgroundColor3 = defaultState and Config.AccentColor or Color3.fromRGB(60, 60, 65)
    SwitchBtn.Text = ""

    SwitchCorner.CornerRadius = UDim.new(1, 0)
    SwitchCorner.Parent = SwitchBtn

    SwitchCircle.Parent = SwitchBtn
    SwitchCircle.Position = defaultState and UDim2.new(1, -17, 0.5, -7) or UDim2.new(0, 3, 0.5, -7)
    SwitchCircle.Size = UDim2.new(0, 14, 0, 14)
    SwitchCircle.BackgroundColor3 = Color3.fromRGB(255, 255, 255)

    CircleCorner.CornerRadius = UDim.new(1, 0)
    CircleCorner.Parent = SwitchCircle

    local state = defaultState
    SwitchBtn.MouseButton1Click:Connect(function()
        state = not state
        SwitchBtn.BackgroundColor3 = state and Config.AccentColor or Color3.fromRGB(60, 60, 65)
        SwitchCircle.Position = state and UDim2.new(1, -17, 0.5, -7) or UDim2.new(0, 3, 0.5, -7)
        callback(state)
    end)
end

local function CreateSliderOption(parentTab, name, minVal, maxVal, defaultVal, callback)
    local Container = Instance.new("Frame")
    local Corner = Instance.new("UICorner")
    local Label = Instance.new("TextLabel")
    local ValueLabel = Instance.new("TextLabel")
    local BtnSub = Instance.new("TextButton")
    local BtnAdd = Instance.new("TextButton")

    Container.Size = UDim2.new(1, -5, 0, 38)
    Container.BackgroundColor3 = Color3.fromRGB(35, 35, 40)
    Container.Parent = parentTab

    Corner.CornerRadius = UDim.new(0, 6)
    Corner.Parent = Container

    Label.Parent = Container
    Label.Position = UDim2.new(0, 10, 0, 0)
    Label.Size = UDim2.new(0.5, 0, 1, 0)
    Label.Text = name
    Label.TextColor3 = Color3.fromRGB(240, 240, 240)
    Label.TextSize = 13
    Label.Font = Enum.Font.SourceSans
    Label.TextXAlignment = Enum.TextXAlignment.Left

    local curVal = defaultVal

    ValueLabel.Parent = Container
    ValueLabel.Position = UDim2.new(1, -100, 0, 0)
    ValueLabel.Size = UDim2.new(0, 40, 1, 0)
    ValueLabel.Text = tostring(curVal)
    ValueLabel.TextColor3 = Config.AccentColor
    ValueLabel.TextSize = 13
    ValueLabel.Font = Enum.Font.SourceSansBold

    BtnSub.Parent = Container
    BtnSub.Position = UDim2.new(1, -130, 0.5, -11)
    BtnSub.Size = UDim2.new(0, 24, 0, 22)
    BtnSub.BackgroundColor3 = Color3.fromRGB(60, 60, 65)
    BtnSub.Text = "-"
    BtnSub.TextColor3 = Color3.fromRGB(255, 255, 255)

    BtnAdd.Parent = Container
    BtnAdd.Position = UDim2.new(1, -55, 0.5, -11)
    BtnAdd.Size = UDim2.new(0, 24, 0, 22)
    BtnAdd.BackgroundColor3 = Color3.fromRGB(60, 60, 65)
    BtnAdd.Text = "+"
    BtnAdd.TextColor3 = Color3.fromRGB(255, 255, 255)

    BtnSub.MouseButton1Click:Connect(function()
        curVal = math.clamp(curVal - 5, minVal, maxVal)
        ValueLabel.Text = tostring(curVal)
        callback(curVal)
    end)

    BtnAdd.MouseButton1Click:Connect(function()
        curVal = math.clamp(curVal + 5, minVal, maxVal)
        ValueLabel.Text = tostring(curVal)
        callback(curVal)
    end)
end

-- TAB 1: ESP
CreateToggleOption(TabESP, "Định Vị Box", Config.ESP_Box, function(v) Config.ESP_Box = v end)
CreateToggleOption(TabESP, "Định Vị Khung Xương", Config.ESP_Skeleton, function(v) Config.ESP_Skeleton = v end)
CreateToggleOption(TabESP, "Định Vị Tia Bảy Màu", Config.ESP_RainbowTracer, function(v) Config.ESP_RainbowTracer = v end)
CreateToggleOption(TabESP, "Định Vị Màu Tùy Chỉnh", Config.ESP_CustomTracer, function(v) Config.ESP_CustomTracer = v end)

-- TAB 2: ABDYEN (AimBot)
CreateToggleOption(TabABDYEN, "AimBot Ghim Đầu", Config.AimHead, function(v) Config.AimHead = v end)
CreateToggleOption(TabABDYEN, "Bắn Đâu Cũng Dính Đầu", Config.MagicBullet, function(v) Config.MagicBullet = v end)
CreateToggleOption(TabABDYEN, "Đạn Thẳng & Auto Vào Đầu", Config.NoSpread, function(v) Config.NoSpread = v end)

-- TAB 3: COLOR
local ColorsList = {
    {Name = "Xanh Dương (Chính)", Color = Color3.fromRGB(0, 170, 255)},
    {Name = "Đỏ Trầm", Color = Color3.fromRGB(255, 50, 50)},
    {Name = "Hồng Neon", Color = Color3.fromRGB(255, 0, 150)},
    {Name = "Xanh Lá Neon", Color = Color3.fromRGB(0, 255, 120)},
    {Name = "Vàng Tươi", Color = Color3.fromRGB(255, 215, 0)},
    {Name = "Tím Sáng", Color = Color3.fromRGB(160, 32, 240)}
}

for _, item in ipairs(ColorsList) do
    local Btn = Instance.new("TextButton")
    local Corner = Instance.new("UICorner")

    Btn.Size = UDim2.new(1, -5, 0, 32)
    Btn.BackgroundColor3 = item.Color
    Btn.Text = "Đổi Màu Menu: " .. item.Name
    Btn.TextColor3 = Color3.fromRGB(255, 255, 255)
    Btn.Font = Enum.Font.SourceSansBold
    Btn.TextSize = 13
    Btn.Parent = TabColor

    Corner.CornerRadius = UDim.new(0, 6)
    Corner.Parent = Btn

    Btn.MouseButton1Click:Connect(function()
        Config.AccentColor = item.Color
        Config.ESPColor = item.Color
        MainStroke.Color = item.Color
        TitleLabel.TextColor3 = item.Color
        ToggleStroke.Color = item.Color
        FOVStroke.Color = item.Color
    end)
end

-- TAB 4: KHÁC
CreateToggleOption(TabKhac, "Hiện Vòng AIM", Config.ShowFOV, function(v)
    Config.ShowFOV = v
    FOVCircle.Visible = v
end)
CreateSliderOption(TabKhac, "Kích Thước Vòng AIM", 20, 500, Config.FOVSize, function(v)
    Config.FOVSize = v
    FOVCircle.Size = UDim2.new(0, v * 2, 0, v * 2)
end)
CreateSliderOption(TabKhac, "Chỉnh Trái / Phải (X)", -300, 300, Config.FOVOffsetX, function(v)
    Config.FOVOffsetX = v
    FOVCircle.Position = UDim2.new(0.5, v, 0.5, Config.FOVOffsetY)
end)
CreateSliderOption(TabKhac, "Chỉnh Lên / Xuống (Y)", -300, 300, Config.FOVOffsetY, function(v)
    Config.FOVOffsetY = v
    FOVCircle.Position = UDim2.new(0.5, Config.FOVOffsetX, 0.5, v)
end)

-- Core Target Function
local function GetClosestPlayerInFOV()
    local closestPlr = nil
    local shortestDist = Config.FOVSize

    for _, plr in pairs(Players:GetPlayers()) do
        if plr ~= LocalPlayer and plr.Character and plr.Character:FindFirstChild("Head") then
            local pos, onScreen = Camera:WorldToViewportPoint(plr.Character.Head.Position)
            if onScreen then
                local center = Vector2.new(Camera.ViewportSize.X/2 + Config.FOVOffsetX, Camera.ViewportSize.Y/2 + Config.FOVOffsetY)
                local dist = (Vector2.new(pos.X, pos.Y) - center).Magnitude
                if dist < shortestDist then
                    shortestDist = dist
                    closestPlr = plr
                end
            end
        end
    end
    return closestPlr
end

-- Folders Visuals
local ESPFolder = Instance.new("Folder", NazilGui)
ESPFolder.Name = "ESP_Visuals"

-- Main Render Loop
RunService.RenderStepped:Connect(function()
    Config.RainbowHue = (Config.RainbowHue + 0.005) % 1
    local CurrentRainbow = Color3.fromHSV(Config.RainbowHue, 1, 1)

    -- Aim Bot Execution
    if Config.AimHead then
        local target = GetClosestPlayerInFOV()
        if target and target.Character and target.Character:FindFirstChild("Head") then
            Camera.CFrame = CFrame.new(Camera.CFrame.Position, target.Character.Head.Position)
        end
    end

    -- Visual Rendering
    ESPFolder:ClearAllChildren()

    for _, plr in pairs(Players:GetPlayers()) do
        if plr ~= LocalPlayer and plr.Character then
            local char = plr.Character
            local hrp = char:FindFirstChild("HumanoidRootPart")

            -- Highlight / Box ESP
            if Config.ESP_Box then
                if not char:FindFirstChild("NazilHighlight") then
                    local hl = Instance.new("Highlight", char)
                    hl.Name = "NazilHighlight"
                    hl.FillColor = Config.ESPColor
                end
            elseif char:FindFirstChild("NazilHighlight") then
                char.NazilHighlight:Destroy()
            end

            -- Tracers Rendering
            if hrp then
                local hrpPos, onScreen = Camera:WorldToViewportPoint(hrp.Position)
                if onScreen then
                    if Config.ESP_RainbowTracer or Config.ESP_CustomTracer then
                        local Line = Instance.new("Frame", ESPFolder)
                        Line.BorderSizePixel = 0
                        Line.BackgroundColor3 = Config.ESP_RainbowTracer and CurrentRainbow or Config.ESPColor
                        
                        local startPos = Vector2.new(Camera.ViewportSize.X / 2, Camera.ViewportSize.Y)
                        local endPos = Vector2.new(hrpPos.X, hrpPos.Y)
                        local dist = (endPos - startPos).Magnitude
                        local angle = math.atan2(endPos.Y - startPos.Y, endPos.X - startPos.X)

                        Line.Size = UDim2.new(0, dist, 0, 1.5)
                        Line.Position = UDim2.new(0, startPos.X, 0, startPos.Y)
                        Line.Rotation = math.deg(angle)
                        Line.AnchorPoint = Vector2.new(0, 0.5)
                    end
                end
            end
        end
    end
end)

-- Magic Bullet Hooking
local rawMetatable = getrawmetatable(game)
local oldNamecall = rawMetatable.__namecall
setreadonly(rawMetatable, false)

rawMetatable.__namecall = newcclosure(function(self, ...)
    local method = getnamecallmethod()
    local args = {...}

    if (Config.MagicBullet or Config.NoSpread) and (method == "Raycast" or method == "FindPartOnRayWithIgnoreList" or method == "FireServer") then
        local target = GetClosestPlayerInFOV()
        if target and target.Character and target.Character:FindFirstChild("Head") then
            if method == "Raycast" then
                args[2] = (target.Character.Head.Position - args[1]).Unit * 1000
            end
        end
    end

    return oldNamecall(self, unpack(args))
end)

setreadonly(rawMetatable, true)

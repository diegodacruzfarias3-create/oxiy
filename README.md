--============================================================
-- Quantum Onyx — Clean Edition
-- Sem key system | Sem Luarmor | Sem bugs
-- Uso: loadstring(game:HttpGet("URL"))()
--============================================================

local Directory = "https://raw.githubusercontent.com/flazhy/QuantumOnyx/refs/heads/main/Games"

local Scripts = {
    Free = {
        [994732206]  = Directory .. "/BloxFruits.lua",
        [9186719164] = Directory .. "/SailorPiece.lua",
        [8191429227] = Directory .. "/CutTrees.lua",
    },
}

--============================================================
-- Serviços
--============================================================
local Players          = game:GetService("Players")
local TweenService     = game:GetService("TweenService")
local UserInputService = game:GetService("UserInputService")
local StarterGui       = game:GetService("StarterGui")
local LocalPlayer      = Players.LocalPlayer
local Mouse            = LocalPlayer:GetMouse()
local GameId           = game.GameId

--============================================================
-- Helpers
--============================================================
local function Tween(obj, props, t, style, dir)
    style = style or Enum.EasingStyle.Quint
    dir   = dir   or Enum.EasingDirection.Out
    TweenService:Create(obj, TweenInfo.new(t, style, dir), props):Play()
end

local function Protect(gui)
    local ok, env = pcall(function() return (getgenv and getgenv()) or _G end)
    if ok and env and typeof(env.HIDEUI) == "Instance" then
        gui.Parent = env.HIDEUI
        return
    end
    if gethui then gui.Parent = gethui() return end
    if syn and syn.protect_gui then
        pcall(function() syn.protect_gui(gui) end)
    end
    gui.Parent = game:GetService("CoreGui")
end

local function New(class, props, parent)
    local inst = Instance.new(class)
    for k, v in pairs(props) do
        if k ~= "Children" and k ~= "Parent" then
            pcall(function() inst[k] = v end)
        end
    end
    if props.Children then
        for _, c in ipairs(props.Children) do
            pcall(function() c.Parent = inst end)
        end
    end
    local target = props.Parent or parent
    if target then pcall(function() inst.Parent = target end) end
    return inst
end

local function Notify(title, desc, duration)
    pcall(function()
        StarterGui:SetCore("SendNotification", {
            Title    = title or "Quantum Onyx",
            Text     = desc  or "",
            Duration = duration or 5,
        })
    end)
end

local function CircleRipple(btn, mx, my)
    task.spawn(function()
        local nx = mx - btn.AbsolutePosition.X
        local ny = my - btn.AbsolutePosition.Y
        local sz = math.max(btn.AbsoluteSize.X, btn.AbsoluteSize.Y) * 1.6
        local c = New("ImageLabel", {
            Name                   = "Ripple",
            Image                  = "rbxassetid://266543268",
            ImageColor3            = Color3.fromRGB(255, 255, 255),
            ImageTransparency      = 0.82,
            BackgroundTransparency = 1,
            ZIndex                 = btn.ZIndex + 5,
            Size                   = UDim2.new(0, 0, 0, 0),
            Position               = UDim2.new(0, nx, 0, ny),
        }, btn)
        Tween(c, { Size = UDim2.new(0, sz, 0, sz), Position = UDim2.new(0.5, -sz/2, 0.5, -sz/2) }, 0.45, Enum.EasingStyle.Quad)
        Tween(c, { ImageTransparency = 1 }, 0.45, Enum.EasingStyle.Linear)
        task.wait(0.46)
        pcall(function() c:Destroy() end)
    end)
end

--============================================================
-- Carregamento de script
--============================================================
local function LoadScript()
    local url = Scripts.Free[GameId]
    if not url then
        Notify("Quantum Onyx", "No script available for this game (ID: " .. tostring(GameId) .. ").", 6)
        return
    end
    local ok, err = pcall(function()
        loadstring(game:HttpGet(url))()
    end)
    if not ok then
        warn("[Quantum Onyx] Load failed: " .. tostring(err))
        Notify("Quantum Onyx", "Failed to load script. Check console.", 6)
    end
end

--============================================================
-- UI
--============================================================
local function ShowUI()
    -- Remove UIs antigas (evita duplicação)
    pcall(function()
        for _, obj in ipairs(game:GetService("CoreGui"):GetChildren()) do
            if obj:IsA("ScreenGui") and obj.Name:match("^QuantumOnyx_") then
                obj:Destroy()
            end
        end
    end)

    local SG = Instance.new("ScreenGui")
    SG.Name           = "QuantumOnyx_" .. tostring(math.random(1e6))
    SG.ZIndexBehavior = Enum.ZIndexBehavior.Global
    SG.ResetOnSpawn   = false
    SG.IgnoreGuiInset = true
    Protect(SG)

    local W, H = 340, 180

    local Card = New("Frame", {
        AnchorPoint      = Vector2.new(0.5, 0.5),
        Position         = UDim2.new(0.5, 0, 0.5, 0),
        Size             = UDim2.new(0, W, 0, H),
        BackgroundColor3 = Color3.fromRGB(15, 12, 24),
        BorderSizePixel  = 0,
        ClipsDescendants = true,
        Parent           = SG,
        Children = {
            New("UICorner", { CornerRadius = UDim.new(0, 12) }),
            New("UIStroke", {
                Color           = Color3.fromRGB(120, 60, 220),
                Transparency    = 0.3,
                Thickness       = 1.5,
                ApplyStrokeMode = Enum.ApplyStrokeMode.Border,
            }),
        },
    })

    New("Frame", {
        BackgroundColor3       = Color3.fromRGB(80, 20, 160),
        BackgroundTransparency = 0.85,
        BorderSizePixel        = 0,
        Position               = UDim2.new(0, -50, 0, -50),
        Size                   = UDim2.new(0, 180, 0, 180),
        Parent                 = Card,
        Children = { New("UICorner", { CornerRadius = UDim.new(1, 0) }) },
    })

    New("TextLabel", {
        BackgroundTransparency = 1,
        Position              = UDim2.new(0, 0, 0, 12),
        Size                  = UDim2.new(1, 0, 0, 26),
        Font                  = Enum.Font.FredokaOne,
        Text                  = "Quantum Onyx",
        TextColor3            = Color3.fromRGB(220, 200, 255),
        TextSize              = 17,
        TextXAlignment        = Enum.TextXAlignment.Center,
        Parent                = Card,
    })

    New("TextLabel", {
        BackgroundTransparency = 1,
        Position              = UDim2.new(0, 0, 0, 38),
        Size                  = UDim2.new(1, 0, 0, 14),
        Font                  = Enum.Font.Gotham,
        Text                  = "Game ID: " .. tostring(GameId),
        TextColor3            = Color3.fromRGB(140, 120, 180),
        TextSize              = 10,
        TextXAlignment        = Enum.TextXAlignment.Center,
        Parent                = Card,
    })

    -- Botão principal
    local ExecBtn = New("TextButton", {
        Position         = UDim2.new(0, 20, 0, 62),
        Size             = UDim2.new(1, -40, 0, 42),
        BackgroundColor3 = Color3.fromRGB(65, 25, 130),
        BorderSizePixel  = 0,
        AutoButtonColor  = false,
        Text             = "",
        ClipsDescendants = true,
        Parent           = Card,
        Children = {
            New("UICorner", { CornerRadius = UDim.new(0, 8) }),
            New("TextLabel", {
                BackgroundTransparency = 1,
                Size                  = UDim2.new(1, 0, 1, 0),
                Font                  = Enum.Font.GothamBold,
                Text                  = "Executar Script",
                TextColor3            = Color3.fromRGB(225, 180, 255),
                TextSize              = 14,
            }),
        },
    })

    ExecBtn.MouseEnter:Connect(function()
        Tween(ExecBtn, { BackgroundColor3 = Color3.fromRGB(85, 35, 160) }, 0.12)
    end)
    ExecBtn.MouseLeave:Connect(function()
        Tween(ExecBtn, { BackgroundColor3 = Color3.fromRGB(65, 25, 130) }, 0.16)
    end)
    ExecBtn.MouseButton1Click:Connect(function()
        CircleRipple(ExecBtn, Mouse.X, Mouse.Y)
        task.wait(0.1)
        LoadScript()
    end)

    -- Botão fechar
    local CloseBtn = New("TextButton", {
        Position         = UDim2.new(0, 20, 1, -46),
        Size             = UDim2.new(1, -40, 0, 30),
        BackgroundColor3 = Color3.fromRGB(45, 15, 25),
        BorderSizePixel  = 0,
        AutoButtonColor  = false,
        Text             = "",
        ClipsDescendants = true,
        Parent           = Card,
        Children = {
            New("UICorner", { CornerRadius = UDim.new(0, 6) }),
            New("TextLabel", {
                BackgroundTransparency = 1,
                Size                  = UDim2.new(1, 0, 1, 0),
                Font                  = Enum.Font.GothamBold,
                Text                  = "Fechar",
                TextColor3            = Color3.fromRGB(255, 130, 150),
                TextSize              = 12,
            }),
        },
    })
    CloseBtn.MouseEnter:Connect(function()
        Tween(CloseBtn, { BackgroundColor3 = Color3.fromRGB(70, 20, 40) }, 0.12)
    end)
    CloseBtn.MouseLeave:Connect(function()
        Tween(CloseBtn, { BackgroundColor3 = Color3.fromRGB(45, 15, 25) }, 0.16)
    end)
    CloseBtn.MouseButton1Click:Connect(function()
        pcall(function() SG:Destroy() end)
    end)
end

--============================================================
-- Execução
--============================================================
ShowUI()

-- Carregar a biblioteca Fluent (interface)
local Fluent = loadstring(game:HttpGet("https://github.com/dawid-scripts/Fluent/raw/main/Fluent.lua"))()

-- Criar janela
local Window = Fluent.CreateWindow({
    Title = "DK Aimbot Mobile",
    Size = UDim2.fromOffset(580, 460),
    Theme = "Dark"
})

-- Abas
local Tabs = {
    Main = Window:AddTab({ Title = "Aimbot" }),
}

-- Variáveis Globais
local FOVRadius = 100 -- Raio inicial do FOV

-- Função: Encontra jogador mais próximo DENTRO do FOV
function GetClosestInFOV()
    local Closest
    local ShortestDistance = math.huge

    for _, Player in ipairs(game.Players:GetPlayers()) do
        if Player ~= game.Players.LocalPlayer and Player.Character and Player.Character:FindFirstChild("HumanoidRootPart") then
            local Root = Player.Character.HumanoidRootPart
            local ScreenPos, OnScreen = workspace.CurrentCamera:WorldToViewportPoint(Root.Position)

            if OnScreen then
                local Mouse = game.Players.LocalPlayer:GetMouse()
                local Distance = (Vector2.new(ScreenPos.X, ScreenPos.Y) - Vector2.new(Mouse.X, Mouse.Y)).Magnitude
                if Distance < ShortestDistance and Distance <= FOVRadius then
                    Closest = Player
                    ShortestDistance = Distance
                end
            end
        end
    end

    return Closest
end

-- Aimbot Loop (automático, sem toggle)
spawn(function()
    while true do
        wait(0.05)
        local Target = GetClosestInFOV()
        if Target and Target.Character and Target.Character:FindFirstChild("HumanoidRootPart") then
            local Root = Target.Character.HumanoidRootPart
            local Pos, OnScreen = workspace.CurrentCamera:WorldToViewportPoint(Root.Position)
            if OnScreen then
                game.Players.LocalPlayer:GetMouse().MoveTo(game.Players.LocalPlayer:GetMouse(), Pos.X, Pos.Y)
            end
        end
    end
end)

-- Slider para ajustar FOV
Tabs.Main:AddSlider({
    Title = "FOV Radius",
    Min = 50,
    Max = 500,
    Default = FOVRadius,
    Callback = function(value)
        FOVRadius = value
    end
})

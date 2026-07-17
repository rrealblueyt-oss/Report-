local spawner = loadstring(game:HttpGet("https://raw.githubusercontent.com/RegularVynixu/Utilities/main/Doors/Entity%20Spawner/V2/Source.lua"))()
local achievementGiver = loadstring(game:HttpGet("https://raw.githubusercontent.com/RegularVynixu/Utilities/main/Doors/Custom%20Achievements/Source.lua"))()

-- hiệu ứng màn hình chuyển màu tím như cũ (giữ nguyên từ script gốc của bạn)
game.Lighting.MainColorCorrection.TintColor = Color3.fromRGB(180, 0, 255)
game.Lighting.MainColorCorrection.Contrast = 1

local tween = game:GetService("TweenService")
tween:Create(game.Lighting.MainColorCorrection, TweenInfo.new(2.5), {Contrast = 0}):Play()
tween:Create(game.Lighting.MainColorCorrection, TweenInfo.new(80), {TintColor = Color3.fromRGB(255,255,255)}):Play()

-- Tạo Entity với các tùy chỉnh mới
local entity = spawner.Create({
    Entity = {
        Name = "Silence",
        Asset = "rbxassetid://12805768832", -- Đã đổi sang ID Model mới của bạn
        HeightOffset = 0
    },
    Lights = {
        Flicker = {
            Enabled = true,
            Duration = 1.5
        },
        Shatter = true,
        Repair = false,
        Color = Color3.fromRGB(255, 0, 0) -- YÊU CẦU: Xuất hiện đèn màu đỏ
    },
    CameraShake = {
        Enabled = false, -- YÊU CẦU: Không động đất (Tắt rung camera)
        Range = 100,
        Values = {2, 25, 0.1, 1}
    },
    Movement = {
        Speed = 99, -- YÊU CẦU: Tốc độ chỉnh thành 99
        Delay = 1,
        Reversed = false
    },
    Rebounding = {
        Enabled = false,
        Type = "Ambush",
        Min = 1,
        Max = 1,
        Delay = 2
    },
    Damage = {
        Enabled = true,
        Range = 50,
        Amount = 9999
    },
    Crucifixion = {
        Enabled = false,
        Range = 40,
        Resist = false,
        Break = true
    },
    Death = {
        Type = "Guiding",
        Hints = {"Your die to silence"},
        Cause = "Die to silence"
    }
})

-- Xử lý sự kiện khi người chơi chết hoặc sống sót để nhận Badge
entity:SetCallback("OnDamagePlayer", function(newHealth)
    local ReSt = game.ReplicatedStorage
    local Plr = game.Players.LocalPlayer
    
    -- Ghi nhận nguyên nhân chết
    if ReSt:FindFirstChild("GameStats") and ReSt.GameStats:FindFirstChild("Player_"..Plr.Name) then
        ReSt.GameStats["Player_"..Plr.Name].Total.DeathCause.Value = "Die to silence"
    end
    
    -- YÊU CẦU: Chết vẫn nhận được Achievement (hoặc tùy biến theo ý bạn)
    achievementGiver({
        Title = "Report Experience",
        Desc = "Look me.",
        Reason = "Me see you.",
        Image = "rbxassetid://82254927036864"
    })
end)

-- Nếu sống sót (Vượt qua thực thể thành công)
entity:SetCallback("OnDespawned", function()
    -- YÊU CẦU: Thắng nhận Achievement
    achievementGiver({
        Title = "Report Experience",
        Desc = "Look me.",
        Reason = "Me see you.",
        Image = "rbxassetid://82254927036864"
    })
end)

-- Chạy Entity
entity:Run()

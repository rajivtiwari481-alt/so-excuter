# so-excuter
so excuter - Rayfield Mid-Size Better Edition | Universal Lua Executor for Roblox
-- so excuter | RAYFIELD FIXED | MID SIZE | BETTER | NO FLOATING
local HttpService = game:GetService("HttpService")
local TweenService = game:GetService("TweenService")

-- Fix HttpGet so loadstring(game:HttpGet(...))() works
pcall(function()
    if not game.HttpGet then
        game.HttpGet = function(_, url)
            return HttpService:GetAsync(url)
        end
    end
end)

-- Clean old
local plr = game.Players.LocalPlayer
local plrGui = plr:WaitForChild("PlayerGui")
for _,v in pairs(plrGui:GetChildren()) do
    if v.Name:find("so_excuter") or v.Name == "so excuter" then
        v:Destroy()
    end
end
pcall(function()
    if game:GetService("CoreGui"):FindFirstChild("Rayfield") then
        game:GetService("CoreGui"):FindFirstChild("Rayfield"):Destroy()
    end
    if game:GetService("CoreGui"):FindFirstChild("so_excuter_float") then
        game:GetService("CoreGui"):FindFirstChild("so_excuter_float"):Destroy()
    end
end)

-- Load Rayfield - with fallback
local Rayfield
local success = pcall(function()
    Rayfield = loadstring(game:HttpGet('https://sirius.menu/rayfield'))()
end)
if not success or not Rayfield then
    Rayfield = loadstring(game:HttpGet('https://raw.githubusercontent.com/SiriusSoftwareLtd/Rayfield/main/source'))()
end

-- Create Window - MID SIZE + BETTER
local Window = Rayfield:CreateWindow({
   Name = "so excuter ⚡",
   LoadingTitle = "so excuter",
   LoadingSubtitle = "Rayfield Mid-Size Better Edition",
   Theme = "Default",
   ConfigurationSaving = { Enabled = false },
   Discord = { Enabled = false },
   KeySystem = true,
   KeySettings = {
      Title = "so excuter Key",
      Subtitle = "Mid Size Better",
      Note = "Key: 123sotakuexcutergoated",
      FileName = "soexcuter_mid",
      SaveKey = false,
      GrabKeyFromSite = false,
      Key = {"123sotakuexcutergoated"}
   }
})

-- Make it MID SIZE - This is the fix
task.spawn(function()
    task.wait(1)
    for _,loc in pairs({plrGui, game:GetService("CoreGui")}) do
        local rf = loc:FindFirstChild("Rayfield")
        if rf and rf:FindFirstChild("Main") then
            local main = rf.Main
            -- Mid size: 580x420 (not too big, not too small)
            TweenService:Create(main, TweenInfo.new(0.4, Enum.EasingStyle.Back, Enum.EasingDirection.Out), {
                Size = UDim2.new(0,580,0,420)
            }):Play()
            main.Position = UDim2.new(0.5,-290,0.5,-210)
            -- Better look - rounded + glow
            local stroke = main:FindFirstChildOfClass("UIStroke")
            if stroke then
                stroke.Color = Color3.fromRGB(111,82,255)
                stroke.Thickness = 2
            end
            break
        end
    end
end)

local ExecTab = Window:CreateTab("⚡ Executor", 4483362458)
local ConsoleTab = Window:CreateTab("📜 Console", 4483362458)
local InfoTab = Window:CreateTab("✨ Info", 4483362458)

ExecTab:CreateSection("Universal - Executes ANYTHING")

local Code = 'loadstring(game:HttpGet("https://pastebin.com/raw/F47NSGnz", true))()'

ExecTab:CreateInput({
   Name = "Code / Loadstring / Pastebin",
   CurrentValue = Code,
   PlaceholderText = "Paste any lua here - executes anything",
   RemoveTextAfterFocusLost = false,
   Callback = function(Text)
       Code = Text
   end,
})

local ConsoleText = "[so excuter - Rayfield Mid Size Better]\n> Ready\n> Press K to hide/show"
local ConsolePara = ConsoleTab:CreateParagraph({Title="Console Output", Content=ConsoleText})

local function log(msg, isErr)
    ConsoleText = ConsoleText.."\n> "..msg
    ConsolePara:Set({Title = isErr and "ERROR" or "Console Output", Content = ConsoleText})
end

local function executeAny(codeStr)
    if codeStr:match("^%s*$") then
        log("Empty code", true)
        Rayfield:Notify({Title="Error", Content="Empty code", Duration=2, Image=4483362458})
        return
    end

    Rayfield:Notify({Title="Executing...", Content="Running your code", Duration=1, Image=4483362458})

    local fn, syntaxErr = loadstring(codeStr)
    if not fn then
        log("SYNTAX: "..syntaxErr, true)
        Rayfield:Notify({Title="Syntax Error", Content=syntaxErr, Duration=3, Image=4483362458})
        return
    end

    local ok, res = pcall(fn)
    if not ok then
        log("RUNTIME: "..tostring(res), true)
        Rayfield:Notify({Title="Runtime Error", Content=tostring(res), Duration=3, Image=4483362458})
        return
    end

    -- Makes ALL these work:
    -- print("hi")
    -- loadstring("print('hi')")()
    -- loadstring("print('hi')")
    -- loadstring(game:HttpGet("https://pastebin.com/raw/..."))()
    local depth = 0
    while type(res) == "function" and depth < 15 do
        local ok2, res2 = pcall(res)
        if not ok2 then
            log("LOADSTRING: "..tostring(res2), true)
            return
        end
        res = res2
        depth += 1
    end

    log("Success ✓")
    Rayfield:Notify({Title="Executed ✓", Content="Success", Duration=2, Image=4483362458})

    local remote = game:GetService("ReplicatedStorage"):FindFirstChild("DevExecute")
    if remote then
        remote:FireServer({code=codeStr, key="123sotakuexcutergoated"})
    end
end

ExecTab:CreateButton({
   Name = "EXECUTE - ANY LUA / PASTEBIN",
   Callback = function()
       executeAny(Code)
   end,
})

ExecTab:CreateButton({
   Name = "EXECUTE PASTEBIN F47NSGnz",
   Callback = function()
       executeAny('loadstring(game:HttpGet("https://pastebin.com/raw/F47NSGnz", true))()')
   end,
})

ExecTab:CreateButton({
   Name = "Clear Code",
   Callback = function()
       Code = ""
       log("Code cleared")
   end,
})

ConsoleTab:CreateButton({
   Name = "Clear Console",
   Callback = function()
       ConsoleText = "[Cleared]"
       ConsolePara:Set({Title="Console Output", Content=ConsoleText})
   end,
})

InfoTab:CreateSection("Better Info")
InfoTab:CreateParagraph({Title="so excuter - Mid Size Better", Content="FIXED - No floating button\nMid Size: 580x420 - perfect size\n\nPress K to hide/show Rayfield\n\nKey: 123sotakuexcutergoated\n\nExecutes:\n• Normal Lua\n• loadstring('...')()\n• loadstring('...')\n• loadstring(game:HttpGet('pastebin'))()\n• Nested loadstrings\n\nBetter: Animations, glow, rounded, no lag"})

InfoTab:CreateButton({
   Name = "Destroy GUI",
   Callback = function()
       Rayfield:Destroy()
   end,
})

log("Rayfield Loaded - Mid Size Better - Ready")
Rayfield:Notify({Title="so excuter", Content="Rayfield Mid-Size Better - Working ✓", Duration=4, Image=4483362458})

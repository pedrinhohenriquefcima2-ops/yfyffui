local HUB_NAME = "Pedro Scripts"

local Players = game:GetService("Players")
local UIS = game:GetService("UserInputService")
local TweenService = game:GetService("TweenService")
local SoundService = game:GetService("SoundService")
local Lighting = game:GetService("Lighting")
local ReplicatedStorage = game:GetService("ReplicatedStorage")

local LP = Players.LocalPlayer

_G.AimbotEnabled = _G.AimbotEnabled or false
_G.ESPEnabled = _G.ESPEnabled or false
_G.TeamCheck = _G.TeamCheck ~= false

local SaveEvent = ReplicatedStorage:WaitForChild("PedroScripts_Save")

local blur = Instance.new("BlurEffect")
blur.Size = 0
blur.Parent = Lighting

local clickSound = Instance.new("Sound")
clickSound.SoundId = "rbxassetid://9118823101"
clickSound.Volume = 0.6
clickSound.Parent = SoundService

local gui = Instance.new("ScreenGui")
gui.Name = "PedroScriptsHub"
gui.ResetOnSpawn = false
gui.Parent = LP:WaitForChild("PlayerGui")

local main = Instance.new("Frame")
main.Size = UDim2.fromScale(0, 0)
main.Position = UDim2.fromScale(0.5, 0.5)
main.BackgroundColor3 = Color3.fromRGB(18, 18, 22)
main.BorderSizePixel = 0
main.Parent = gui

TweenService:Create(main, TweenInfo.new(0.45, Enum.EasingStyle.Quart, Enum.EasingDirection.Out), {
	Size = UDim2.fromScale(0.6, 0.64),
	Position = UDim2.fromScale(0.2, 0.18)
}):Play()

TweenService:Create(blur, TweenInfo.new(0.35), {Size = 16}):Play()

local corner = Instance.new("UICorner", main)
corner.CornerRadius = UDim.new(0, 22)

local stroke = Instance.new("UIStroke", main)
stroke.Color = Color3.fromRGB(220, 60, 60)
stroke.Thickness = 3
stroke.Transparency = 0.05

local overlay = Instance.new("Frame")
overlay.Size = UDim2.fromScale(1, 1)
overlay.BackgroundColor3 = Color3.fromRGB(12, 12, 16)
overlay.BackgroundTransparency = 0.05
overlay.BorderSizePixel = 0
overlay.Parent = main

local grad = Instance.new("UIGradient", overlay)
grad.Color = ColorSequence.new{
	ColorSequenceKeypoint.new(0, Color3.fromRGB(120, 25, 25)),
	ColorSequenceKeypoint.new(1, Color3.fromRGB(15, 0, 0))
}
grad.Rotation = 90

local oc = Instance.new("UICorner", overlay)
oc.CornerRadius = UDim.new(0, 22)

local header = Instance.new("Frame")
header.Size = UDim2.fromScale(1, 0.12)
header.BackgroundTransparency = 1
header.Parent = overlay

local title = Instance.new("TextLabel")
title.Text = HUB_NAME
title.Font = Enum.Font.GothamBlack
title.TextScaled = true
title.TextColor3 = Color3.fromRGB(255, 80, 80)
title.BackgroundTransparency = 1
title.Size = UDim2.fromScale(0.6, 1)
title.Position = UDim2.fromScale(0.04, 0)
title.Parent = header

local btnMin = Instance.new("TextButton")
btnMin.Text = "—"
btnMin.Font = Enum.Font.GothamBold
btnMin.TextScaled = true
btnMin.TextColor3 = Color3.fromRGB(255, 255, 255)
btnMin.BackgroundColor3 = Color3.fromRGB(40, 40, 48)
btnMin.Size = UDim2.fromScale(0.07, 0.6)
btnMin.Position = UDim2.fromScale(0.82, 0.2)
btnMin.Parent = header

local minC = Instance.new("UICorner", btnMin)
minC.CornerRadius = UDim.new(0, 10)

local btnClose = Instance.new("TextButton")
btnClose.Text = "✕"
btnClose.Font = Enum.Font.GothamBold
btnClose.TextScaled = true
btnClose.TextColor3 = Color3.fromRGB(255, 255, 255)
btnClose.BackgroundColor3 = Color3.fromRGB(200, 40, 40)
btnClose.Size = UDim2.fromScale(0.07, 0.6)
btnClose.Position = UDim2.fromScale(0.9, 0.2)
btnClose.Parent = header

local closeC = Instance.new("UICorner", btnClose)
closeC.CornerRadius = UDim.new(0, 10)

local sidebar = Instance.new("Frame")
sidebar.Size = UDim2.fromScale(0.23, 0.88)
sidebar.Position = UDim2.fromScale(0, 0.12)
sidebar.BackgroundColor3 = Color3.fromRGB(20, 20, 26)
sidebar.BorderSizePixel = 0
sidebar.Parent = overlay

local sc = Instance.new("UICorner", sidebar)
sc.CornerRadius = UDim.new(0, 18)

local tabsLayout = Instance.new("UIListLayout")
tabsLayout.Padding = UDim.new(0, 10)
tabsLayout.HorizontalAlignment = Enum.HorizontalAlignment.Center
tabsLayout.Parent = sidebar

local content = Instance.new("Frame")
content.Size = UDim2.fromScale(0.77, 0.88)
content.Position = UDim2.fromScale(0.23, 0.12)
content.BackgroundTransparency = 1
content.Parent = overlay

local notify = Instance.new("TextLabel")
notify.Text = ""
notify.Font = Enum.Font.GothamBold
notify.TextScaled = true
notify.TextColor3 = Color3.fromRGB(255,255,255)
notify.BackgroundColor3 = Color3.fromRGB(200,40,40)
notify.Size = UDim2.fromScale(0.3,0.06)
notify.Position = UDim2.fromScale(0.35,0.9)
notify.BackgroundTransparency = 1
notify.Parent = overlay

local nc = Instance.new("UICorner", notify)
nc.CornerRadius = UDim.new(0,14)

local function toast(text)
	notify.Text = text
	notify.BackgroundTransparency = 0
	TweenService:Create(notify, TweenInfo.new(0.2), {Position = UDim2.fromScale(0.35,0.84)}):Play()
	task.delay(1.2, function()
		TweenService:Create(notify, TweenInfo.new(0.2), {BackgroundTransparency = 1, Position = UDim2.fromScale(0.35,0.9)}):Play()
	end)
end

local pages = {}

local function createPage(name)
	local page = Instance.new("Frame")
	page.Name = name
	page.Size = UDim2.fromScale(1, 1)
	page.BackgroundTransparency = 1
	page.Visible = false
	page.Parent = content
	pages[name] = page

	local pad = Instance.new("UIPadding", page)
	pad.PaddingTop = UDim.new(0, 14)
	pad.PaddingLeft = UDim.new(0, 14)
	pad.PaddingRight = UDim.new(0, 14)
	pad.PaddingBottom = UDim.new(0, 14)

	local list = Instance.new("UIListLayout", page)
	list.Padding = UDim.new(0, 12)

	return page
end

local function createTab(text, pageName)
	local btn = Instance.new("TextButton")
	btn.Text = text
	btn.Font = Enum.Font.GothamBold
	btn.TextScaled = true
	btn.TextColor3 = Color3.fromRGB(245, 245, 245)
	btn.BackgroundColor3 = Color3.fromRGB(32, 32, 40)
	btn.Size = UDim2.fromScale(0.9, 0.1)
	btn.Parent = sidebar

	local c = Instance.new("UICorner", btn)
	c.CornerRadius = UDim.new(0, 14)

	btn.MouseButton1Click:Connect(function()
		clickSound:Play()
		TweenService:Create(btn, TweenInfo.new(0.12), {Size = UDim2.fromScale(0.88, 0.1)}):Play()
		task.delay(0.12, function()
			TweenService:Create(btn, TweenInfo.new(0.12), {Size = UDim2.fromScale(0.9, 0.1)}):Play()
		end)
		for _, p in pairs(pages) do p.Visible = false end
		pages[pageName].Visible = true
	end)
end

local function toggleItem(parent, label, default, callback)
	local wrap = Instance.new("Frame")
	wrap.Size = UDim2.fromScale(1, 0.12)
	wrap.BackgroundColor3 = Color3.fromRGB(26, 26, 34)
	wrap.Parent = parent

	local c = Instance.new("UICorner", wrap)
	c.CornerRadius = UDim.new(0, 14)

	local stroke = Instance.new("UIStroke", wrap)
	stroke.Color = Color3.fromRGB(180, 50, 50)
	stroke.Transparency = 0.5

	local txt = Instance.new("TextLabel")
	txt.Text = label
	txt.Font = Enum.Font.Gotham
	txt.TextScaled = true
	txt.TextColor3 = Color3.fromRGB(245, 245, 245)
	txt.BackgroundTransparency = 1
	txt.Size = UDim2.fromScale(0.68, 1)
	txt.Parent = wrap

	local btn = Instance.new("TextButton")
	btn.Text = default and "ON" or "OFF"
	btn.Font = Enum.Font.GothamBold
	btn.TextScaled = true
	btn.TextColor3 = Color3.fromRGB(255,255,255)
	btn.BackgroundColor3 = default and Color3.fromRGB(200, 40, 40) or Color3.fromRGB(70, 70, 80)
	btn.Size = UDim2.fromScale(0.26, 0.7)
	btn.Position = UDim2.fromScale(0.7, 0.15)
	btn.Parent = wrap

	local bc = Instance.new("UICorner", btn)
	bc.CornerRadius = UDim.new(0, 12)

	local state = default
	btn.MouseButton1Click:Connect(function()
		clickSound:Play()
		state = not state
		btn.Text = state and "ON" or "OFF"
		TweenService:Create(btn, TweenInfo.new(0.15), {BackgroundColor3 = state and Color3.fromRGB(200, 40, 40) or Color3.fromRGB(70, 70, 80)}):Play()
		callback(state)
	end)
end

local pageAimbot = createPage("Aimbot")
local pageESP = createPage("ESP")
local pageSettings = createPage("Settings")

createTab("Aimbot", "Aimbot")
createTab("ESP", "ESP")
createTab("Config", "Settings")

pages.Aimbot.Visible = true

toggleItem(pageAimbot, "Aimbot (Q)", _G.AimbotEnabled, function(v)
	_G.AimbotEnabled = v
	SaveEvent:FireServer(_G.AimbotEnabled, _G.ESPEnabled, _G.TeamCheck)
	toast("Config salva")
end)

toggleItem(pageESP, "ESP (F)", _G.ESPEnabled, function(v)
	_G.ESPEnabled = v
	SaveEvent:FireServer(_G.AimbotEnabled, _G.ESPEnabled, _G.TeamCheck)
	toast("Config salva")
end)

toggleItem(pageESP, "Team Check", _G.TeamCheck, function(v)
	_G.TeamCheck = v
	SaveEvent:FireServer(_G.AimbotEnabled, _G.ESPEnabled, _G.TeamCheck)
	toast("Config salva")
end)

toggleItem(pageSettings, "Salvar Configurações", false, function()
	SaveEvent:FireServer(_G.AimbotEnabled, _G.ESPEnabled, _G.TeamCheck)
	toast("Config salva")
end)

local minimized = false
local normalSize = main.Size
local normalPos = main.Position

btnMin.MouseButton1Click:Connect(function()
	clickSound:Play()
	minimized = not minimized
	if minimized then
		TweenService:Create(main, TweenInfo.new(0.25), {Size = UDim2.fromScale(0.22, 0.12), Position = UDim2.fromScale(0.39, 0.03)}):Play()
	else
		TweenService:Create(main, TweenInfo.new(0.25), {Size = normalSize, Position = normalPos}):Play()
	end
end)

btnClose.MouseButton1Click:Connect(function()
	clickSound:Play()
	TweenService:Create(main, TweenInfo.new(0.25), {Size = UDim2.fromScale(0, 0), Position = UDim2.fromScale(0.5, 0.5)}):Play()
	TweenService:Create(blur, TweenInfo.new(0.25), {Size = 0}):Play()
	task.delay(0.25, function()
		gui.Enabled = false
	end)
end)

local dragging, dragStart, startPos

header.InputBegan:Connect(function(input)
	if input.UserInputType == Enum.UserInputType.MouseButton1 then
		dragging = true
		dragStart = input.Position
		startPos = main.Position
	end
end)

header.InputEnded:Connect(function(input)
	if input.UserInputType == Enum.UserInputType.MouseButton1 then
		dragging = false
	end
end)

UIS.InputChanged:Connect(function(input)
	if dragging and input.UserInputType == Enum.UserInputType.MouseMovement then
		local delta = input.Position - dragStart
		main.Position = UDim2.new(startPos.X.Scale, startPos.X.Offset + delta.X, startPos.Y.Scale, startPos.Y.Offset + delta.Y)
	end
end)

UIS.InputBegan:Connect(function(input, gp)
	if gp then return end
	if input.KeyCode == Enum.KeyCode.H then
		gui.Enabled = not gui.Enabled
		TweenService:Create(blur, TweenInfo.new(0.25), {Size = gui.Enabled and 16 or 0}):Play()
	end
	if input.KeyCode == Enum.KeyCode.Q then _G.AimbotEnabled = not _G.AimbotEnabled end
	if input.KeyCode == Enum.KeyCode.F then _G.ESPEnabled = not _G.ESPEnabled end
end)

-- SERVER SCRIPT (ServerScriptService)
local DSS = game:GetService("DataStoreService"):GetDataStore("PedroScriptsConfig")
local RS = game:GetService("ReplicatedStorage")

local ev = Instance.new("RemoteEvent")
ev.Name = "PedroScripts_Save"
ev.Parent = RS

ev.OnServerEvent:Connect(function(plr, a, e, t)
	DSS:SetAsync(plr.UserId, {A=a, E=e, T=t})
end)

Players.PlayerAdded:Connect(function(plr)
	local data = DSS:GetAsync(plr.UserId)
	if data then
		plr:SetAttribute("PS_Aimbot", data.A)
		plr:SetAttribute("PS_ESP", data.E)
		plr:SetAttribute("PS_TeamCheck", data.T)
	end
end)

-- ===== PREMIUM ADD-ONS (SAFE / UI / SYSTEMS) =====

-- Theme system
local Themes = {
	Red = {Primary=Color3.fromRGB(200,40,40), Bg=Color3.fromRGB(18,18,22)},
	Dark = {Primary=Color3.fromRGB(120,120,120), Bg=Color3.fromRGB(14,14,18)},
	Neon = {Primary=Color3.fromRGB(255,80,120), Bg=Color3.fromRGB(20,10,20)}
}

local function applyTheme(t)
	stroke.Color = t.Primary
	overlay.BackgroundColor3 = t.Bg
	title.TextColor3 = t.Primary
end

-- FPS counter (visual only)
local fpsLabel = Instance.new("TextLabel")
fpsLabel.Text = "FPS: --"
fpsLabel.Font = Enum.Font.GothamBold
fpsLabel.TextScaled = true
fpsLabel.TextColor3 = Color3.fromRGB(255,255,255)
fpsLabel.BackgroundTransparency = 1
fpsLabel.Size = UDim2.fromScale(0.18,0.06)
fpsLabel.Position = UDim2.fromScale(0.78,0.9)
fpsLabel.Parent = overlay

local last = os.clock()
local frames = 0

game:GetService("RunService").RenderStepped:Connect(function()
	frames += 1
	local now = os.clock()
	if now - last >= 1 then
		fpsLabel.Text = "FPS: "..frames
		frames = 0
		last = now
	end
end)

	toast("Key inválida")
end

-- Loader animation
local loader = Instance.new("Frame")
loader.Size = UDim2.fromScale(1,1)
loader.BackgroundColor3 = Color3.fromRGB(10,10,14)
loader.Parent = gui

local loadTxt = Instance.new("TextLabel")
loadTxt.Text = "Pedro Scripts | Loading"
loadTxt.Font = Enum.Font.GothamBlack
loadTxt.TextScaled = true
loadTxt.TextColor3 = Color3.fromRGB(255,80,80)
loadTxt.BackgroundTransparency = 1
loadTxt.Size = UDim2.fromScale(0.5,0.1)
loadTxt.Position = UDim2.fromScale(0.25,0.45)
loadTxt.Parent = loader

TweenService:Create(loadTxt, TweenInfo.new(0.6, Enum.EasingStyle.Sine, Enum.EasingDirection.InOut, -1, true), {
	TextTransparency = 0.3
}):Play()

task.delay(1.5, function()
	TweenService:Create(loader, TweenInfo.new(0.35), {BackgroundTransparency = 1}):Play()
	loadTxt.Visible = false
	task.delay(0.35, function() loader:Destroy() end)
end)

-- Settings extras
applyTheme(Themes.Red)

-- Credits page
local pageCredits = createPage("Credits")
createTab("Créditos", "Credits")
local cTxt = Instance.new("TextLabel")
cTxt.Text = "Pedro Scripts
Premium Hub
UI • Systems • Performance"
cTxt.Font = Enum.Font.GothamBold
cTxt.TextScaled = true
cTxt.TextColor3 = Color3.fromRGB(240,240,240)
cTxt.BackgroundTransparency = 1
cTxt.Size = UDim2.fromScale(1,1)
cTxt.Parent = pageCredits

-- Anti-spam toggle protection
local lastToggle = 0
local function guarded(cb)
	return function(v)
		if os.clock() - lastToggle < 0.15 then return end
		lastToggle = os.clock()
		cb(v)
	end
end


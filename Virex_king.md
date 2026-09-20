--// VIREX HUB SAE
--// Red Theme + Speed Slider + Auto Collect + Anti-Hit

local Players = game:GetService("Players")
local ProximityPromptService = game:GetService("ProximityPromptService")
local RunService = game:GetService("RunService")
local TweenService = game:GetService("TweenService")
local UserInputService = game:GetService("UserInputService")

local LocalPlayer = Players.LocalPlayer
local PlayerGui = LocalPlayer:WaitForChild("PlayerGui")

--// SETTINGS
-- Replace YOUR_VIREX_LOGO_ID with the Roblox asset ID of your VIREX logo.
local LogoId = "rbxassetid://YOUR_VIREX_LOGO_ID"

local targetParent = (gethui and gethui()) or PlayerGui

--// REMOVE OLD GUI
local oldGui = targetParent:FindFirstChild("VirexHubGui")
if oldGui then
	oldGui:Destroy()
end

--// STATE
local AntiHitEnabled = false
local SpeedOverrideEnabled = false
local AutoCollectEnabled = false

local TargetSpeed = 700
local IsTeleporting = false
local MenuOpen = true
local SliderDragging = false

--// COLORS
local RED = Color3.fromRGB(220, 40, 55)
local LIGHT_RED = Color3.fromRGB(255, 130, 140)
local DARK_RED = Color3.fromRGB(150, 25, 40)

local BACKGROUND = Color3.fromRGB(15, 15, 22)
local BUTTON = Color3.fromRGB(30, 30, 42)
local TEXT = Color3.fromRGB(240, 240, 245)

--// TELEPORT POINTS
local TeleportPoints = {
	Vector3.new(500.62, 241.28, -366.64),
	Vector3.new(504.45, 155.80, -366.35),
	Vector3.new(508.30, 70.28, -366.03),
	Vector3.new(513.86, 70.28, -366.25),
	Vector3.new(519.43, 70.28, -366.47),
	Vector3.new(524.32, 70.28, -366.59),
	Vector3.new(529.22, 70.28, -366.71),
	Vector3.new(538.01, 70.28, -365.55),
	Vector3.new(546.80, 70.28, -364.40)
}

--// GUI
local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Name = "VirexHubGui"
ScreenGui.ResetOnSpawn = false
ScreenGui.ZIndexBehavior = Enum.ZIndexBehavior.Sibling
ScreenGui.Parent = targetParent

--// OPEN/CLOSE BUTTON
local MenuButton = Instance.new("TextButton")
MenuButton.Name = "MenuButton"
MenuButton.Parent = ScreenGui
MenuButton.Size = UDim2.new(0, 45, 0, 45)
MenuButton.Position = UDim2.new(0, 15, 0.5, -22)
MenuButton.BackgroundColor3 = BACKGROUND
MenuButton.BackgroundTransparency = 0.1
MenuButton.Text = "X"
MenuButton.TextColor3 = LIGHT_RED
MenuButton.TextSize = 22
MenuButton.Font = Enum.Font.GothamBold
MenuButton.AutoButtonColor = false
MenuButton.ZIndex = 30

local MenuCorner = Instance.new("UICorner")
MenuCorner.CornerRadius = UDim.new(0, 10)
MenuCorner.Parent = MenuButton

local MenuStroke = Instance.new("UIStroke")
MenuStroke.Color = RED
MenuStroke.Thickness = 2
MenuStroke.Parent = MenuButton

--// MAIN FRAME
local MainFrame = Instance.new("Frame")
MainFrame.Name = "MainFrame"
MainFrame.Parent = ScreenGui
MainFrame.Size = UDim2.new(0, 240, 0, 225)
MainFrame.Position = UDim2.new(0.5, -120, 0.5, -112)
MainFrame.BackgroundColor3 = BACKGROUND
MainFrame.BackgroundTransparency = 0.35
MainFrame.BorderSizePixel = 0
MainFrame.ZIndex = 1

local MainCorner = Instance.new("UICorner")
MainCorner.CornerRadius = UDim.new(0, 10)
MainCorner.Parent = MainFrame

local MainStroke = Instance.new("UIStroke")
MainStroke.Color = RED
MainStroke.Thickness = 1.5
MainStroke.Parent = MainFrame

--// BACKGROUND IMAGE
local BackgroundImage = Instance.new("ImageLabel")
BackgroundImage.Name = "VirexBackgroundImage"
BackgroundImage.Parent = MainFrame
BackgroundImage.Size = UDim2.new(1, 0, 1, 0)
BackgroundImage.BackgroundTransparency = 1
BackgroundImage.Image = LogoId
BackgroundImage.ImageTransparency = 0.85
BackgroundImage.ScaleType = Enum.ScaleType.Crop
BackgroundImage.ZIndex = 0

local BackgroundCorner = Instance.new("UICorner")
BackgroundCorner.CornerRadius = UDim.new(0, 10)
BackgroundCorner.Parent = BackgroundImage

--// TOP BAR
local TopBar = Instance.new("Frame")
TopBar.Parent = MainFrame
TopBar.Size = UDim2.new(1, 0, 0, 40)
TopBar.BackgroundTransparency = 1
TopBar.ZIndex = 2

--// LOGO
local Logo = Instance.new("ImageLabel")
Logo.Parent = TopBar
Logo.Size = UDim2.new(0, 30, 0, 30)
Logo.Position = UDim2.new(0, 7, 0, 5)
Logo.BackgroundTransparency = 1
Logo.Image = LogoId
Logo.ZIndex = 3

--// TITLE
local Title = Instance.new("TextLabel")
Title.Parent = TopBar
Title.Size = UDim2.new(0, 145, 0, 30)
Title.Position = UDim2.new(0, 42, 0, 5)
Title.BackgroundTransparency = 1
Title.Text = "VIREX HUB SAE"
Title.TextColor3 = LIGHT_RED
Title.TextSize = 15
Title.Font = Enum.Font.GothamBold
Title.TextXAlignment = Enum.TextXAlignment.Left
Title.ZIndex = 3

--// CLOSE BUTTON
local CloseButton = Instance.new("TextButton")
CloseButton.Parent = TopBar
CloseButton.Size = UDim2.new(0, 28, 0, 28)
CloseButton.Position = UDim2.new(1, -35, 0, 6)
CloseButton.BackgroundColor3 = DARK_RED
CloseButton.Text = "X"
CloseButton.TextColor3 = Color3.fromRGB(255, 255, 255)
CloseButton.TextSize = 14
CloseButton.Font = Enum.Font.GothamBold
CloseButton.AutoButtonColor = false
CloseButton.ZIndex = 4

local CloseCorner = Instance.new("UICorner")
CloseCorner.CornerRadius = UDim.new(0, 7)
CloseCorner.Parent = CloseButton

--// TABS
local MainTab = Instance.new("TextButton")
MainTab.Parent = MainFrame
MainTab.Size = UDim2.new(0, 105, 0, 28)
MainTab.Position = UDim2.new(0, 10, 0, 45)
MainTab.BackgroundColor3 = RED
MainTab.Text = "Main"
MainTab.TextColor3 = Color3.fromRGB(255, 255, 255)
MainTab.TextSize = 13
MainTab.Font = Enum.Font.GothamBold
MainTab.AutoButtonColor = false
MainTab.ZIndex = 3

local MainTabCorner = Instance.new("UICorner")
MainTabCorner.CornerRadius = UDim.new(0, 7)
MainTabCorner.Parent = MainTab

local MiscTab = Instance.new("TextButton")
MiscTab.Parent = MainFrame
MiscTab.Size = UDim2.new(0, 105, 0, 28)
MiscTab.Position = UDim2.new(0, 122, 0, 45)
MiscTab.BackgroundColor3 = BUTTON
MiscTab.Text = "Misc"
MiscTab.TextColor3 = TEXT
MiscTab.TextSize = 13
MiscTab.Font = Enum.Font.GothamBold
MiscTab.AutoButtonColor = false
MiscTab.ZIndex = 3

local MiscCorner = Instance.new("UICorner")
MiscCorner.CornerRadius = UDim.new(0, 7)
MiscCorner.Parent = MiscTab

--// CONTENT
local Content = Instance.new("Frame")
Content.Parent = MainFrame
Content.Size = UDim2.new(1, -20, 0, 137)
Content.Position = UDim2.new(0, 10, 0, 78)
Content.BackgroundTransparency = 1
Content.ZIndex = 3

--// ANTI-HIT
local AntiHitButton = Instance.new("TextButton")
AntiHitButton.Parent = Content
AntiHitButton.Size = UDim2.new(1, 0, 0, 35)
AntiHitButton.Position = UDim2.new(0, 0, 0, 0)
AntiHitButton.BackgroundColor3 = BUTTON
AntiHitButton.Text = "Anti-Hit : OFF"
AntiHitButton.TextColor3 = TEXT
AntiHitButton.TextSize = 13
AntiHitButton.Font = Enum.Font.GothamBold
AntiHitButton.AutoButtonColor = false

local AntiHitCorner = Instance.new("UICorner")
AntiHitCorner.CornerRadius = UDim.new(0, 7)
AntiHitCorner.Parent = AntiHitButton

--// SPEED BUTTON
local SpeedButton = Instance.new("TextButton")
SpeedButton.Parent = Content
SpeedButton.Size = UDim2.new(1, 0, 0, 35)
SpeedButton.Position = UDim2.new(0, 0, 0, 43)
SpeedButton.BackgroundColor3 = BUTTON
SpeedButton.Text = "Speed : 700 | OFF"
SpeedButton.TextColor3 = TEXT
SpeedButton.TextSize = 13
SpeedButton.Font = Enum.Font.GothamBold
SpeedButton.AutoButtonColor = false

local SpeedCorner = Instance.new("UICorner")
SpeedCorner.CornerRadius = UDim.new(0, 7)
SpeedCorner.Parent = SpeedButton

--// SPEED SLIDER
local SpeedSliderBack = Instance.new("Frame")
SpeedSliderBack.Parent = Content
SpeedSliderBack.Size = UDim2.new(1, 0, 0, 8)
SpeedSliderBack.Position = UDim2.new(0, 0, 0, 82)
SpeedSliderBack.BackgroundColor3 = Color3.fromRGB(55, 55, 65)
SpeedSliderBack.BorderSizePixel = 0

local SpeedSliderCorner = Instance.new("UICorner")
SpeedSliderCorner.CornerRadius = UDim.new(1, 0)
SpeedSliderCorner.Parent = SpeedSliderBack

local SpeedSliderFill = Instance.new("Frame")
SpeedSliderFill.Parent = SpeedSliderBack
SpeedSliderFill.Size = UDim2.new(TargetSpeed / 1300, 0, 1, 0)
SpeedSliderFill.BackgroundColor3 = RED
SpeedSliderFill.BorderSizePixel = 0

local SpeedFillCorner = Instance.new("UICorner")
SpeedFillCorner.CornerRadius = UDim.new(1, 0)
SpeedFillCorner.Parent = SpeedSliderFill

local SpeedSliderKnob = Instance.new("TextButton")
SpeedSliderKnob.Parent = SpeedSliderBack
SpeedSliderKnob.Size = UDim2.new(0, 16, 0, 16)
SpeedSliderKnob.Position = UDim2.new(TargetSpeed / 1300, -8, 0.5, -8)
SpeedSliderKnob.BackgroundColor3 = LIGHT_RED
SpeedSliderKnob.Text = ""
SpeedSliderKnob.AutoButtonColor = false

local KnobCorner = Instance.new("UICorner")
KnobCorner.CornerRadius = UDim.new(1, 0)
KnobCorner.Parent = SpeedSliderKnob

--// AUTO COLLECT
local AutoCollectButton = Instance.new("TextButton")
AutoCollectButton.Parent = Content
AutoCollectButton.Size = UDim2.new(1, 0, 0, 35)
AutoCollectButton.Position = UDim2.new(0, 0, 0, 98)
AutoCollectButton.BackgroundColor3 = BUTTON
AutoCollectButton.Text = "Auto Collect : OFF"
AutoCollectButton.TextColor3 = TEXT
AutoCollectButton.TextSize = 13
AutoCollectButton.Font = Enum.Font.GothamBold
AutoCollectButton.AutoButtonColor = false

local AutoCollectCorner = Instance.new("UICorner")
AutoCollectCorner.CornerRadius = UDim.new(0, 7)
AutoCollectCorner.Parent = AutoCollectButton

--// MISC CONTENT
local MiscContent = Instance.new("Frame")
MiscContent.Parent = MainFrame
MiscContent.Size = Content.Size
MiscContent.Position = Content.Position
MiscContent.BackgroundTransparency = 1
MiscContent.Visible = false
MiscContent.ZIndex = 3

--// BACKGROUND SELECTOR
local BackgroundButton = Instance.new("TextButton")
BackgroundButton.Parent = MiscContent
BackgroundButton.Size = UDim2.new(1, 0, 0, 35)
BackgroundButton.Position = UDim2.new(0, 0, 0, 0)
BackgroundButton.BackgroundColor3 = BUTTON
BackgroundButton.Text = "Background : Virex"
BackgroundButton.TextColor3 = TEXT
BackgroundButton.TextSize = 13
BackgroundButton.Font = Enum.Font.GothamBold
BackgroundButton.AutoButtonColor = false

local BackgroundCorner = Instance.new("UICorner")
BackgroundCorner.CornerRadius = UDim.new(0, 7)
BackgroundCorner.Parent = BackgroundButton

--// SCALE
local ScaleButton = Instance.new("TextButton")
ScaleButton.Parent = MiscContent
ScaleButton.Size = UDim2.new(1, 0, 0, 35)
ScaleButton.Position = UDim2.new(0, 0, 0, 43)
ScaleButton.BackgroundColor3 = BUTTON
ScaleButton.Text = "GUI Scale : 1.0x"
ScaleButton.TextColor3 = TEXT
ScaleButton.TextSize = 13
ScaleButton.Font = Enum.Font.GothamBold
ScaleButton.AutoButtonColor = false

local ScaleCorner = Instance.new("UICorner")
ScaleCorner.CornerRadius = UDim.new(0, 7)
ScaleCorner.Parent = ScaleButton

--// TAB SWITCHING
MainTab.MouseButton1Click:Connect(function()
	MainTab.BackgroundColor3 = RED
	MiscTab.BackgroundColor3 = BUTTON

	Content.Visible = true
	MiscContent.Visible = false
end)

MiscTab.MouseButton1Click:Connect(function()
	MiscTab.BackgroundColor3 = RED
	MainTab.BackgroundColor3 = BUTTON

	Content.Visible = false
	MiscContent.Visible = true
end)

--// ANTI-HIT TOGGLE
AntiHitButton.MouseButton1Click:Connect(function()
	AntiHitEnabled = not AntiHitEnabled

	if AntiHitEnabled then
		AntiHitButton.Text = "Anti-Hit : ON"
		AntiHitButton.BackgroundColor3 = RED
	else
		AntiHitButton.Text = "Anti-Hit : OFF"
		AntiHitButton.BackgroundColor3 = BUTTON
	end
end)

--// SPEED TOGGLE
SpeedButton.MouseButton1Click:Connect(function()
	SpeedOverrideEnabled = not SpeedOverrideEnabled

	if SpeedOverrideEnabled then
		SpeedButton.Text = "Speed : " .. TargetSpeed .. " | ON"
		SpeedButton.BackgroundColor3 = RED
	else
		SpeedButton.Text = "Speed : " .. TargetSpeed .. " | OFF"
		SpeedButton.BackgroundColor3 = BUTTON

		local Character = LocalPlayer.Character

		if Character then
			local Humanoid = Character:FindFirstChildOfClass("Humanoid")

			if Humanoid then
				Humanoid.WalkSpeed = 16
			end
		end
	end
end)

--// SPEED SLIDER
local function UpdateSpeedSlider(inputX)
	local absoluteX = SpeedSliderBack.AbsolutePosition.X
	local absoluteWidth = SpeedSliderBack.AbsoluteSize.X

	if absoluteWidth <= 0 then
		return
	end

	local percent = math.clamp(
		(inputX - absoluteX) / absoluteWidth,
		0,
		1
	)

	TargetSpeed = math.floor(percent * 1300 + 0.5)

	SpeedSliderFill.Size = UDim2.new(percent, 0, 1, 0)
	SpeedSliderKnob.Position = UDim2.new(percent, -8, 0.5, -8)

	SpeedButton.Text =
		"Speed : " ..
		TargetSpeed ..
		" | " ..
		(SpeedOverrideEnabled and "ON" or "OFF")
end

SpeedSliderKnob.InputBegan:Connect(function(input)
	if input.UserInputType == Enum.UserInputType.MouseButton1
		or input.UserInputType == Enum.UserInputType.Touch then

		SliderDragging = true
	end
end)

SpeedSliderKnob.InputEnded:Connect(function(input)
	if input.UserInputType == Enum.UserInputType.MouseButton1
		or input.UserInputType == Enum.UserInputType.Touch then

		SliderDragging = false
	end
end)

SpeedSliderBack.InputBegan:Connect(function(input)
	if input.UserInputType == Enum.UserInputType.MouseButton1
		or input.UserInputType == Enum.UserInputType.Touch then

		SliderDragging = true
		UpdateSpeedSlider(input.Position.X)
	end
end)

UserInputService.InputChanged:Connect(function(input)
	if not SliderDragging then
		return
	end

	if input.UserInputType == Enum.UserInputType.MouseMovement
		or input.UserInputType == Enum.UserInputType.Touch then

		UpdateSpeedSlider(input.Position.X)
	end
end)

UserInputService.InputEnded:Connect(function(input)
	if input.UserInputType == Enum.UserInputType.MouseButton1
		or input.UserInputType == Enum.UserInputType.Touch then

		SliderDragging = false
	end
end)

--// SPEED LOOP
RunService.Heartbeat:Connect(function()
	if not SpeedOverrideEnabled then
		return
	end

	local Character = LocalPlayer.Character

	if not Character then
		return
	end

	local Humanoid = Character:FindFirstChildOfClass("Humanoid")

	if Humanoid then
		Humanoid.WalkSpeed = TargetSpeed
	end
end)

--// AUTO COLLECT / STEAL-STYLE PROMPTS
-- Only interacts with prompts whose text indicates collecting/stealing.
local function IsCollectPrompt(prompt)
	if not prompt:IsA("ProximityPrompt") then
		return false
	end

	local actionText = string.lower(prompt.ActionText or "")
	local objectText = string.lower(prompt.ObjectText or "")

	local keywords = {
		"collect",
		"take",
		"pickup",
		"pick up",
		"steal"
	}

	for _, keyword in ipairs(keywords) do
		if string.find(actionText, keyword, 1, true)
			or string.find(objectText, keyword, 1, true) then

			return true
		end
	end

	return false
end

local function TryCollectPrompt(prompt)
	if not AutoCollectEnabled then
		return
	end

	if not IsCollectPrompt(prompt) then
		return
	end

	local Character = LocalPlayer.Character

	if not Character then
		return
	end

	local Root = Character:FindFirstChild("HumanoidRootPart")

	if not Root then
		return
	end

	local PromptParent = prompt.Parent

	if not PromptParent then
		return
	end

	local PromptPart

	if PromptParent:IsA("BasePart") then
		PromptPart = PromptParent
	elseif PromptParent:IsA("Attachment") then
		PromptPart = PromptParent.Parent
	end

	if not PromptPart or not PromptPart:IsA("BasePart") then
		return
	end

	local Distance = (Root.Position - PromptPart.Position).Magnitude

	if Distance <= prompt.MaxActivationDistance then
		pcall(function()
			prompt:InputHoldBegin()
			task.wait(math.max(prompt.HoldDuration, 0.05))
			prompt:InputHoldEnd()
		end)
	end
end

AutoCollectButton.MouseButton1Click:Connect(function()
	AutoCollectEnabled = not AutoCollectEnabled

	if AutoCollectEnabled then
		AutoCollectButton.Text = "Auto Collect : ON"
		AutoCollectButton.BackgroundColor3 = RED
	else
		AutoCollectButton.Text = "Auto Collect : OFF"
		AutoCollectButton.BackgroundColor3 = BUTTON
	end
end)

ProximityPromptService.PromptShown:Connect(function(prompt)
	if AutoCollectEnabled then
		TryCollectPrompt(prompt)
	end
end)

--// BACKGROUND SELECTOR
local BackgroundMode = 1

BackgroundButton.MouseButton1Click:Connect(function()
	BackgroundMode += 1

	if BackgroundMode > 3 then
		BackgroundMode = 1
	end

	if BackgroundMode == 1 then
		BackgroundButton.Text = "Background : Virex"
		BackgroundImage.Visible = true
		MainFrame.BackgroundTransparency = 0.35

	elseif BackgroundMode == 2 then
		BackgroundButton.Text = "Background : Plain Black"
		BackgroundImage.Visible = false
		MainFrame.BackgroundTransparency = 0.05

	elseif BackgroundMode == 3 then
		BackgroundButton.Text = "Background : Transparent"
		BackgroundImage.Visible = false
		MainFrame.BackgroundTransparency = 0.75
	end
end)

--// GUI SCALE
local ScaleOptions = {
	0.8,
	1.0,
	1.2,
	1.4
}

local ScaleIndex = 2

ScaleButton.MouseButton1Click:Connect(function()
	ScaleIndex += 1

	if ScaleIndex > #ScaleOptions then
		ScaleIndex = 1
	end

	local Scale = ScaleOptions[ScaleIndex]

	ScaleButton.Text = "GUI Scale : " .. Scale .. "x"

	MainFrame.Size = UDim2.new(
		0,
		240 * Scale,
		0,
		225 * Scale
	)

	MainFrame.Position = UDim2.new(
		0.5,
		-(120 * Scale),
		0.5,
		-(112.5 * Scale)
	)
end)

--// DRAGGING
local dragging = false
local dragStart
local startPos

TopBar.InputBegan:Connect(function(input)
	if input.UserInputType == Enum.UserInputType.MouseButton1
		or input.UserInputType == Enum.UserInputType.Touch then

		dragging = true
		dragStart = input.Position
		startPos = MainFrame.Position

		input.Changed:Connect(function()
			if input.UserInputState == Enum.UserInputState.End then
				dragging = false
			end
		end)
	end
end)

TopBar.InputChanged:Connect(function(input)
	if input.UserInputType == Enum.UserInputType.MouseMovement
		or input.UserInputType == Enum.UserInputType.Touch then

		local dragInput = input

		dragInput.Changed:Connect(function()
			if dragging then
				local delta = dragInput.Position - dragStart

				MainFrame.Position = UDim2.new(
					startPos.X.Scale,
					startPos.X.Offset + delta.X,
					startPos.Y.Scale,
					startPos.Y.Offset + delta.Y
				)
			end
		end)
	end
end)

--// OPEN/CLOSE MENU
local function SetMenuVisible(visible)
	MenuOpen = visible

	if visible then
		MainFrame.Visible = true
		MenuButton.Text = "X"
	else
		MainFrame.Visible = false
		MenuButton.Text = "V"
	end
end

MenuButton.MouseButton1Click:Connect(function()
	SetMenuVisible(not MenuOpen)
end)

CloseButton.MouseButton1Click:Connect(function()
	SetMenuVisible(false)
end)

--// TELEPORT OVERLAY
local TeleportOverlay = Instance.new("Frame")
TeleportOverlay.Parent = ScreenGui
TeleportOverlay.Size = UDim2.new(1, 0, 1, 0)
TeleportOverlay.BackgroundColor3 = Color3.fromRGB(0, 0, 0)
TeleportOverlay.BackgroundTransparency = 0.25
TeleportOverlay.Visible = false
TeleportOverlay.ZIndex = 20

local OverlayLogo = Instance.new("ImageLabel")
OverlayLogo.Parent = TeleportOverlay
OverlayLogo.Size = UDim2.new(0, 70, 0, 70)
OverlayLogo.Position = UDim2.new(0.5, -35, 0.5, -80)
OverlayLogo.BackgroundTransparency = 1
OverlayLogo.Image = LogoId
OverlayLogo.ZIndex = 21

local OverlayText = Instance.new("TextLabel")
OverlayText.Parent = TeleportOverlay
OverlayText.Size = UDim2.new(0, 300, 0, 40)
OverlayText.Position = UDim2.new(0.5, -150, 0.5, 0)
OverlayText.BackgroundTransparency = 1
OverlayText.Text = "anti hit almost loaded"
OverlayText.TextColor3 = Color3.fromRGB(255, 255, 255)
OverlayText.TextSize = 16
OverlayText.Font = Enum.Font.GothamBold
OverlayText.ZIndex = 21

local ProgressBack = Instance.new("Frame")
ProgressBack.Parent = TeleportOverlay
ProgressBack.Size = UDim2.new(0, 240, 0, 8)
ProgressBack.Position = UDim2.new(0.5, -120, 0.5, 45)
ProgressBack.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
ProgressBack.BorderSizePixel = 0
ProgressBack.ZIndex = 21

local ProgressCorner = Instance.new("UICorner")
ProgressCorner.CornerRadius = UDim.new(1, 0)
ProgressCorner.Parent = ProgressBack

local ProgressFill = Instance.new("Frame")
ProgressFill.Parent = ProgressBack
ProgressFill.Size = UDim2.new(0, 0, 1, 0)
ProgressFill.BackgroundColor3 = RED
ProgressFill.BorderSizePixel = 0
ProgressFill.ZIndex = 22

local ProgressFillCorner = Instance.new("UICorner")
ProgressFillCorner.CornerRadius = UDim.new(1, 0)
ProgressFillCorner.Parent = ProgressFill

--// TELEPORT FUNCTION
local function TeleportRoute(Character)
	if IsTeleporting then
		return
	end

	IsTeleporting = true

	TeleportOverlay.Visible = true
	ProgressFill.Size = UDim2.new(0, 0, 1, 0)
	OverlayText.Text = "anti hit almost loaded"

	for i, Position in ipairs(TeleportPoints) do
		if not Character or not Character.Parent then
			break
		end

		Character:PivotTo(CFrame.new(Position))

		local Progress = i / #TeleportPoints

		TweenService:Create(
			ProgressFill,
			TweenInfo.new(0.15),
			{
				Size = UDim2.new(Progress, 0, 1, 0)
			}
		):Play()

		task.wait(0.15)
	end

	OverlayText.Text = "anti hit loaded!"

	task.wait(0.6)

	local Fade = TweenService:Create(
		TeleportOverlay,
		TweenInfo.new(0.4),
		{
			BackgroundTransparency = 1
		}
	)

	Fade:Play()
	Fade.Completed:Wait()

	TeleportOverlay.Visible = false
	TeleportOverlay.BackgroundTransparency = 0.25

	IsTeleporting = false
end

--// PROXIMITY PROMPT
ProximityPromptService.PromptTriggered:Connect(function(prompt, player)
	if player ~= LocalPlayer then
		return
	end

	if not AntiHitEnabled then
		return
	end

	local Character = LocalPlayer.Character

	if Character then
		TeleportRoute(Character)
	end
end)

--// CHARACTER RESPAWN
LocalPlayer.CharacterAdded:Connect(function(Character)
	task.wait(1)

	if SpeedOverrideEnabled then
		local Humanoid = Character:FindFirstChildOfClass("Humanoid")

		if Humanoid then
			Humanoid.WalkSpeed = TargetSpeed
		end
	end
end)

print("VIREX HUB SAE loaded.")

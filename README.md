-- Force script name
script.Name = "PKL Lagger!"

-- Services
local P = game:GetService("Players")
local R = game:GetService("RunService")
local U = game:GetService("UserInputService")
local T = game:GetService("TweenService")
local p = P.LocalPlayer

-- UI
local g = Instance.new("ScreenGui", p.PlayerGui)
g.ResetOnSpawn = false
g.IgnoreGuiInset = true

-- Main frame
local f = Instance.new("Frame", g)
f.Size = UDim2.fromScale(0.13, 0.16)
f.Position = UDim2.fromScale(0.5, 0.5)
f.AnchorPoint = Vector2.new(0.5, 0.5)
f.BackgroundColor3 = Color3.fromRGB(10, 10, 10)
f.BackgroundTransparency = 1
f.BorderSizePixel = 0
f.Active = true
Instance.new("UICorner", f).CornerRadius = UDim.new(0, 10)

-- UIScale popup
local scale = Instance.new("UIScale", f)
scale.Scale = 0.65

-- Stroke
local stroke = Instance.new("UIStroke", f)
stroke.Color = Color3.fromRGB(70, 140, 255)
stroke.Thickness = 1.2
stroke.Transparency = 1

-- Title
local title = Instance.new("TextLabel", f)
title.Size = UDim2.fromScale(1, 0.22)
title.BackgroundTransparency = 1
title.Text = "PKL"
title.TextScaled = true
title.TextColor3 = Color3.fromRGB(220, 230, 255)
title.TextTransparency = 1
title.Font = Enum.Font.GothamBold
title.Active = true

-- Popup animation
task.defer(function()
	T:Create(scale, TweenInfo.new(0.45, Enum.EasingStyle.Back), { Scale = 1 }):Play()
	T:Create(f, TweenInfo.new(0.25), { BackgroundTransparency = 0 }):Play()
	T:Create(stroke, TweenInfo.new(0.25), { Transparency = 0.15 }):Play()
	T:Create(title, TweenInfo.new(0.25), { TextTransparency = 0 }):Play()
end)

-- Dragging
local dragging, ds, sp = false
title.InputBegan:Connect(function(i)
	if i.UserInputType == Enum.UserInputType.MouseButton1 or i.UserInputType == Enum.UserInputType.Touch then
		dragging = true
		ds = i.Position
		sp = f.Position
	end
end)

title.InputEnded:Connect(function(i)
	if i.UserInputType == Enum.UserInputType.MouseButton1 or i.UserInputType == Enum.UserInputType.Touch then
		dragging = false
	end
end)

U.InputChanged:Connect(function(i)
	if dragging and (i.UserInputType == Enum.UserInputType.MouseMovement or i.UserInputType == Enum.UserInputType.Touch) then
		local x = i.Position - ds
		f.Position = UDim2.new(sp.X.Scale, sp.X.Offset + x.X, sp.Y.Scale, sp.Y.Offset + x.Y)
	end
end)

-- 📉 Slider bar
local bar = Instance.new("Frame", f)
bar.Size = UDim2.fromScale(0.9, 0.08)
bar.Position = UDim2.fromScale(0.05, 0.28)
bar.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
Instance.new("UICorner", bar).CornerRadius = UDim.new(1, 0)

local fill = Instance.new("Frame", bar)
fill.Size = UDim2.fromScale(0.4, 1)
fill.BackgroundColor3 = Color3.fromRGB(70, 140, 255)
Instance.new("UICorner", fill).CornerRadius = UDim.new(1, 0)

local knob = Instance.new("Frame", bar)
knob.Size = UDim2.fromScale(0.08, 1.4)
knob.Position = UDim2.fromScale(0.4, -0.2)
knob.BackgroundColor3 = Color3.fromRGB(220, 230, 255)
Instance.new("UICorner", knob).CornerRadius = UDim.new(1, 0)

-- NUKE button
local nuke = Instance.new("TextButton", f)
nuke.Size = UDim2.fromScale(0.9, 0.38)
nuke.Position = UDim2.fromScale(0.05, 0.52)
nuke.Text = "NUKE"
nuke.TextScaled = true
nuke.Font = Enum.Font.GothamBlack
nuke.BackgroundColor3 = Color3.fromRGB(90, 10, 10)
nuke.TextColor3 = Color3.fromRGB(255, 230, 230)
nuke.AutoButtonColor = false
Instance.new("UICorner", nuke).CornerRadius = UDim.new(0, 8)

-- State
local mode = "OFF"
local minLoad = 200000
local maxLoad = 3000000
local load = 1000000
local sliding = false
local nuked = false

-- Slider logic
local function updateSlider(x)
	local rel = math.clamp((x - bar.AbsolutePosition.X) / bar.AbsoluteSize.X, 0, 1)
	fill.Size = UDim2.fromScale(rel, 1)
	knob.Position = UDim2.fromScale(rel, -0.2)
	load = math.floor(minLoad + (maxLoad - minLoad) * rel)
end

bar.InputBegan:Connect(function(i)
	if i.UserInputType == Enum.UserInputType.MouseButton1 or i.UserInputType == Enum.UserInputType.Touch then
		sliding = true
		updateSlider(i.Position.X)
	end
end)

U.InputChanged:Connect(function(i)
	if sliding and (i.UserInputType == Enum.UserInputType.MouseMovement or i.UserInputType == Enum.UserInputType.Touch) then
		updateSlider(i.Position.X)
	end
end)

U.InputEnded:Connect(function(i)
	if i.UserInputType == Enum.UserInputType.MouseButton1 or i.UserInputType == Enum.UserInputType.Touch then
		sliding = false
	end
end)

-- 💣 NUKE
nuke.MouseButton1Click:Connect(function()
	if nuked then return end
	nuked = true
	mode = "NUKE"
	nuke.Text = "i just took your shi ty"

	task.delay(5, function()
		p:Kick("i just took your shi ty")
	end)
end)

-- Render lag loop
R.RenderStepped:Connect(function()
	if mode ~= "NUKE" then return end
	for i = 1, load do
		math.sqrt(i * math.random())
	end
	task.wait(5)
end)

-- Background spam
task.spawn(function()
	while true do
		if mode == "NUKE" then
			for i = 1, 200 do
				task.spawn(function()
					for j = 1, 40000 do
						math.sin(j)
					end
				end)
			end
		end
		task.wait(0.02)
	end
end)

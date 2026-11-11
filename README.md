local SU = "https://raw.githubusercontent.com/PulseZax/Slate/refs/heads/main/.lua"
local S = loadstring(game:HttpGet(SU), "@Slate")()
pcall(function() S.Cleanup() end)
pcall(function() S:PreloadIcons({ "lucide" }) end)
local TEMA_ROXO = {
Background = Color3.fromRGB(18, 12, 26),
Surface = Color3.fromRGB(23, 16, 33),
SurfaceAlt = Color3.fromRGB(30, 20, 43),
Elevated = Color3.fromRGB(36, 24, 52),
Border = Color3.fromRGB(100, 60, 160),
Line = Color3.fromRGB(60, 38, 88),
Text = Color3.fromRGB(240, 232, 250),
Muted = Color3.fromRGB(160, 132, 190),
Faint = Color3.fromRGB(110, 88, 140),
Accent = Color3.fromRGB(168, 100, 255),
OnAccent = Color3.fromRGB(18, 10, 30),
Success = Color3.fromRGB(120, 220, 160),
Warning = Color3.fromRGB(240, 190, 90),
Danger = Color3.fromRGB(245, 90, 120),
ToggleOn = Color3.fromRGB(168, 100, 255),
ToggleKnob = Color3.fromRGB(250, 246, 255),
Info = Color3.fromRGB(130, 170, 255),
}
pcall(function() if S.Theme and S.Theme.Set then S.Theme:Set(TEMA_ROXO) end end)
pcall(function() S.Theme.Preset("Violet") end)
pcall(function() S:SetFontFamily("JosefinSans") end)
local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local UserInputService = game:GetService("UserInputService")
local TweenService = game:GetService("TweenService")
local Lighting = game:GetService("Lighting")
local GuiService = game:GetService("GuiService")
local TeleportService = game:GetService("TeleportService")
local CollectionService = game:GetService("CollectionService")
local HttpService = game:GetService("HttpService")
local ReplicatedStorage = game:GetService("ReplicatedStorage")
local LocalPlayer = Players.LocalPlayer
local C = (getgenv and getgenv()) or _G
cloneref = cloneref or function(v) return v end
gethui = gethui or get_hidden_gui
protect_gui = protect_gui or protectgui or (syn and syn.protect_gui)
if C.__VETERANO_SINTONIA_CLEANUP then pcall(C.__VETERANO_SINTONIA_CLEANUP) end
local P = 1500
local T = {
monitorarStaff = false, sorte2xEnabled = false,
espEnabled = false, box = false, name = false, distance = false, heldItem = false,
skeleton = false, highlight = false, tracer = false, arrow = false, box3d = false, teamTag = false,
maxDistance = P, bundles = {}, spoofWalkEnabled = false, spoofJumpEnabled = false,
spoofWalkSpeed = 40, spoofJumpPower = 90,
walkSpeedKeybind = Enum.KeyCode.V, menuKeybind = Enum.KeyCode.K,
noclipEnabled = false, noclipKeybind = Enum.KeyCode.N,
flyEnabled = false, flyKeybind = Enum.KeyCode.F, flySpeed = 60,
ghostEnabled = false,
aimbotEnabled = false, aimbotMode = "Legit", aimbotFov = 120, aimbotDistance = 500,
aimbotVisibleOnly = false, aimbotFovVisible = false,
aimbotHitChance = 100, aimbotBodyPart = "Cabeca", aimbotSmoothness = 0.15,
aimbotTargetMode = "Closest", aimbotWallCheck = true, aimbotTeamCheck = true,
aimbotKeybind = Enum.KeyCode.E, friendCheck = true, adminCheck = true,
armaTracer = false, armaTracerCor = "Ciano",
perfectLockpick = false, returnOnWeaponPickup = true,
pullWeaponsEnabled = false,
autoFarmLixeiroEnabled = false, autoFarmLixeiroQuantidade = 20,
farmPartesEnabled = false,
durabilidadeHudEnabled = false,
autoCintoEnabled = false,
noclipCarEnabled = false, vFlyEnabled = false,
vFlyHorizontalSpeed = 60, vFlyVerticalSpeed = 30,
boostFactor = 1, fullbright = false, noLag = false,
autoFarmPescaEnabled = false,
autoFarmPumpkinEnabled = false, pumpkinEspEnabled = false,
autoColetaEnabled = false, autoColetaCooldown = 0.35,
coletaHudEnabled = false, coletaYOffset = 3, coletaReachDistance = 4,
camuflarNickEnabled = false,
carEspEnabled = false, carEspDistance = 1500,
}
local o = {
connections = {}, placeMatched = tostring(game.PlaceId) == "1252599131646017",
destroyed = false,
itemTeleportRunId = 0, farmPartesRunId = 0, farmPartesColetadas = 0,
garbageFarmRunId = 0, garbageFarmStatus = "Parado",
durabilidadeRunId = 0,
relationships = {}, relationshipConnections = {}, staffCount = 0, staffNames = {},
pescaRunId = 0, pescaStatus = "Parado", pescaColetados = 0,
coletaRunId = 0, coletaHudRunId = 0,
}
C.__VETERANO_SINTONIA_RUNTIME = o
o.state = T
local function handleReady(conn) table.insert(o.connections, conn); return conn end
local function Xz(msg)
pcall(function()
S:Notify({ Title = "VETERANO TEAM", Description = tostring(msg), Duration = 3, Tone = "Info" })
end)
end
local function isCValid()
local c = LocalPlayer.Character
return c and c:FindFirstChildOfClass("Humanoid") or nil
end
local function isRoot()
local c = LocalPlayer.Character
return c and c:FindFirstChild("HumanoidRootPart")
end
do
local function setupACBypass()
local filtergc = getgenv and getgenv().filtergc or _G and _G.filtergc
local getupvalues = debug and debug.getupvalues
local hookfunction = hookfunction or (syn and syn.hookfunction)
local newcclosure = newcclosure or (syn and syn.newcclosure)
if not (filtergc and getupvalues) then
return
end
local Runtime = o
Runtime.acSilenciados = 0
Runtime.acMotivosVivos = nil
Runtime.acStatus = ""
local poisoned = setmetatable({}, { __mode = "k" })
local function attemptPoison()
local ok, funcs = pcall(filtergc, "function",
{ Constants = { "FireServer", "bor", "clock" } }, false)
if not ok then return 0 end
local count = 0
for _, fn in ipairs(funcs) do
local src = debug.info(fn, "s")
if type(src) == "string" and src:sub(-9) == ".ClientAC" then
local upvals = getupvalues(fn)
local teleportTable = upvals[4]
local target = upvals[1]
if type(target) == "table" and type(teleportTable) == "table"
and rawget(target, "Teleporte") == 1 and not poisoned[teleportTable] then
for k in pairs(target) do
teleportTable[k] = math.huge
end
poisoned[teleportTable] = true
count = count + 1
end
end
end
return count
end
local function attemptHook()
if not hookfunction or not newcclosure then return 0 end
local ok, funcs = pcall(filtergc, "function",
{ Constants = { "FireServer", "bor", "clock" } }, false)
if not ok then return 0 end
local count = 0
for _, fn in ipairs(funcs) do
if type(fn) == "function" then
local okHook = pcall(hookfunction, fn, newcclosure(function(...)
return nil
end))
if okHook then count = count + 1 end
end
end
return count
end
local running = false
local function applyBypass(source)
if running or Runtime.destroyed then return end
running = true
Runtime.acSilenciados = 0
Runtime.acMotivosVivos = nil
Runtime.acStatus = "AC v2: aplicando (" .. source .. ")..."
task.spawn(function()
local total = 0
for _ = 1, 120 do
if Runtime.destroyed then break end
total = total + attemptPoison() + attemptHook()
if total > 0 then break end
task.wait(0.25)
end
running = false
if total > 0 then
Runtime.acSilenciados = total
Runtime.acMotivosVivos = {}
Runtime.acStatus = "AC v2: reports do ClientAC cortados (" .. source
.. ") — valor cru liberado."
else
Runtime.acStatus = "AC v2: Reporta nao encontrado (" .. source .. ") — AC intacto."
end
end)
end
applyBypass("carga")
local LocalPlayer = game:GetService("Players").LocalPlayer
handleReady(LocalPlayer.CharacterAdded:Connect(function()
applyBypass("respawn")
end))
return Runtime
end
local Runtime = setupACBypass()
end
local Jz = {}
do
local rankMin = 254
local friendCol = Color3.fromRGB(60, 210, 255)
local normCol = Color3.fromRGB(170, 70, 255)
local staffCol = Color3.fromRGB(255, 20, 40)
local keywords = { "admin","staff","moder","owner","founder","creator","developer",
"dono","fundador","gerente","supervis","diretor","gestor" }
local function hasKeyword(s)
s = string.lower(tostring(s or ""))
for _, k in ipairs(keywords) do
if string.find(s, k, 1, true) then return true end
end
return false
end
local function isTruthy(v)
local t = typeof(v)
if t == "boolean" then return v end
if t == "number" then return v > 0 end
if t == "string" then
local s = string.lower(v)
return s ~= "" and s ~= "false" and s ~= "0" and s ~= "none" and s ~= "guest" and s ~= "member"
end
return v ~= nil
end
local function isConfiguredStaff(plr)
local list = C.VETERANO_SINTONIA_STAFF_USER_IDS
if type(list) ~= "table" then return false end
return list[plr.UserId] == true or list[tostring(plr.UserId)] == true or table.find(list, plr.UserId) ~= nil
end
local function isAdminRaw(plr)
if isConfiguredStaff(plr) then return true, "configured" end
if game.CreatorType == Enum.CreatorType.User and plr.UserId == game.CreatorId then
return true, "creator"
end
local okA, attrs = pcall(function() return plr:GetAttributes() end)
if okA then
for k, v in pairs(attrs) do
if hasKeyword(k) and isTruthy(v) then return true, "attribute:" .. k end
end
end
local okT, tags = pcall(CollectionService.GetTags, CollectionService, plr)
if okT then
for _, tag in ipairs(tags) do
if hasKeyword(tag) then return true, "tag:" .. tag end
end
end
return false, nil
end
local function getRel(plr)
local r = o.relationships[plr]
if r then return r end
r = { ready = false, checking = false, friend = false, admin = false, rank = 0, role = "", source = nil }
o.relationships[plr] = r
return r
end
local function checkReady(plr)
if plr == LocalPlayer or plr.Parent ~= Players then return end
local rel = getRel(plr)
if rel.checking then return end
rel.checking = true
task.spawn(function()
local friend = false
local okF, res = pcall(function() return LocalPlayer:IsFriendsWith(plr.UserId) end)
if okF then friend = res == true end
local admin, src = isAdminRaw(plr)
local rank, role = 0, ""
if game.CreatorType == Enum.CreatorType.Group and game.CreatorId > 0 then
local okR, r = pcall(function() return plr:GetRankInGroup(game.CreatorId) end)
if okR and type(r) == "number" then rank = r end
local okRo, ro = pcall(function() return plr:GetRoleInGroup(game.CreatorId) end)
if okRo and type(ro) == "string" then role = ro end
if rank >= rankMin then admin, src = true, "group-rank:" .. rank
elseif hasKeyword(role) then admin, src = true, "group-role:" .. role end
end
if plr.Parent == Players and o.relationships[plr] == rel then
rel.friend = friend
rel.admin = admin
rel.rank = rank
rel.role = role
rel.source = src
rel.ready = true
rel.checking = false
end
end)
end
local function getRelSafe(plr)
local r = getRel(plr)
if not r.ready and not r.checking then checkReady(plr) end
return r
end
function Jz.Protected(plr)
local r = getRelSafe(plr)
if (T.friendCheck or T.adminCheck) and not r.ready then return true end
if T.adminCheck and r.admin then return true end
if T.friendCheck and r.friend then return true end
return false
end
Jz.Admin = function(plr)
local r = getRelSafe(plr)
return r.ready and r.admin
end
Jz.Appearance = function(plr)
local r = getRelSafe(plr)
if r.ready and (T.adminCheck and r.admin) then return staffCol, " [STAFF]" end
if r.ready and (T.friendCheck and r.friend) then return friendCol, " [AMIGO]" end
return normCol, ""
end
local function onAdded(plr)
if plr == LocalPlayer then return end
checkReady(plr)
local conn = plr.AttributeChanged:Connect(function(name)
if hasKeyword(name) then
local r = o.relationships[plr]
if r then r.ready = false end
checkReady(plr)
end
end)
o.relationshipConnections[plr] = conn
handleReady(conn)
end
for _, plr in ipairs(Players:GetPlayers()) do onAdded(plr) end
handleReady(Players.PlayerAdded:Connect(onAdded))
handleReady(Players.PlayerRemoving:Connect(function(plr)
o.relationships[plr] = nil
o.relationshipConnections[plr] = nil
end))
end
local U = Vector3.zero
task.spawn(function()
while not o.destroyed do
local hum = isCValid()
if hum then U = hum.MoveDirection end
task.wait(0.05)
end
end)
local L = 28
local function isUpdateInstancePropertiesEValid()
return (o.acSilenciados or 0) > 0 and (o.acMotivosVivos == nil or #o.acMotivosVivos == 0)
end
local b = {
[Enum.HumanoidStateType.Seated] = true,
[Enum.HumanoidStateType.Dead] = true,
[Enum.HumanoidStateType.Climbing] = true,
}
local y = 0.5
local x = 18
local c = 34
local l = 36
local Z = 0.08
local D = 16
local function handleG(arr, now)
local sum = 0
for i = #arr, 1, -1 do
if now - arr[i].t > y then
table.remove(arr, i)
else
sum = sum + arr[i].passo
end
end
return sum
end
local function handleInstanceProperties(arr, now, from, to)
if not to then return end
local d = (to - from).Magnitude
if d > 0 then
table.insert(arr, { t = now, passo = d })
end
end
local j = { root = nil, posicao = nil, ultimaAmostra = 0, historico = {} }
local function updateG(root, now)
local st = j
if st.root ~= root or now - st.ultimaAmostra > y then
st.root = root
st.posicao = root.Position
st.ultimaAmostra = now
table.clear(st.historico)
return 0
end
handleInstanceProperties(st.historico, now, st.posicao, root.Position)
st.posicao = root.Position
st.ultimaAmostra = now
return handleG(st.historico, now)
end
local function isGhValid(root, dt)
local hum = isCValid()
if root.Anchored or not hum or hum.Health <= 0 or dt >= Z then return true end
local st = hum:GetState()
return root.AssemblyLinearVelocity.Magnitude < 12
and (st ~= Enum.HumanoidStateType.Freefall and st ~= Enum.HumanoidStateType.Jumping)
end
local function handleQ(root, dt, targetVel, isFly)
local step = math.clamp(dt or 0.016666, 0.004166, y)
local moved = updateG(root, os.clock())
local stable = not isFly or isGhValid(root, step)
local slow = isFly and (stable and moved > x)
local cap = isFly and (stable and x or c) or x
if slow then cap = c end
local slack = math.max(0, cap - moved)
local vel = math.min(math.max(targetVel or 0, 0), slack / step)
local phys = math.max(0, slack - vel * step)
if slow then phys = 0 end
return step, vel, phys, moved, stable
end
o.handleQ = handleQ
local w, a, i, d = 4, 8, 105, 65
local G, t, K = 0.05, 0.6, 14
local Cz, Yz, gz = 20, 14, 0.55
local mz = Vector3.zero
local Ez, Mz, Hz, Az = 0, 0, 4, 0
local function Fz(dt)
if not T.spoofWalkEnabled then Az = 0; return end
if T.flyEnabled or T.autoFarmPescaEnabled or T.farmPartesEnabled then Az = 0; return end
local root = isRoot()
if not root then return end
local hum = isCValid()
if not hum then return end
if hum.Sit or b[hum:GetState()] then Az = 0; return end
if U.Magnitude <= 0 then
Az = 0
if os.clock() < Ez and mz.Magnitude > 0 then
local ws = math.max(hum.WalkSpeed, 1)
local frac = math.clamp(Yz / ws, 0, 1)
pcall(hum.Move, hum, mz * frac, false)
end
return
end
mz = U.Unit
Ez = os.clock() + gz
local vel = root.AssemblyLinearVelocity
local horiz = Vector3.new(vel.X, 0, vel.Z).Magnitude
if (dt or 0) >= G or root.Anchored or horiz < 12 then
Mz = os.clock() + t
end
local unblocked = isUpdateInstancePropertiesEValid()
local speed = math.min(T.spoofWalkSpeed, unblocked and T.spoofWalkSpeed or (os.clock() < Mz and d or i))
local diff = speed - L
if diff <= 0 then Az = 0; return end
local jitter = 0.9 + math.random() * 0.1
local want = (diff * dt) * jitter + Az
local step = math.min(want, diff * dt, unblocked and a or w)
Az = math.clamp(want - step, 0, Hz)
local dir = U.Unit
root.CFrame = root.CFrame + dir * step
local v = root.AssemblyLinearVelocity
if Vector3.new(v.X, 0, v.Z).Magnitude < K then
root.AssemblyLinearVelocity = Vector3.new(dir.X * Cz, v.Y, dir.Z * Cz)
end
end
o.Fz = Fz
do
local tracked, saved
local function restore()
if tracked and saved then
pcall(function()
tracked.UseJumpPower = saved.UseJumpPower
tracked.JumpPower = saved.JumpPower
tracked.JumpHeight = saved.JumpHeight
tracked:SetStateEnabled(Enum.HumanoidStateType.Jumping, saved.Jumping)
end)
end
tracked, saved = nil, nil
end
function o.updateJumpPower(hum)
if tracked ~= hum then restore() end
if not T.spoofJumpEnabled or o.destroyed or not hum then
restore()
return
end
if not saved then
tracked = hum
saved = {
UseJumpPower = hum.UseJumpPower,
JumpPower = hum.JumpPower,
JumpHeight = hum.JumpHeight,
Jumping = hum:GetStateEnabled(Enum.HumanoidStateType.Jumping),
}
end
local power = math.clamp(tonumber(T.spoofJumpPower) or 90, 0, 1000)
if not hum.UseJumpPower then hum.UseJumpPower = true end
if hum.JumpPower ~= power then hum.JumpPower = power end
if not T.flyEnabled and not T.ghostEnabled and not hum.Sit then
hum:SetStateEnabled(Enum.HumanoidStateType.Jumping, true)
end
end
function o.setJumpPower(v)
T.spoofJumpEnabled = v == true
o.updateJumpPower(isCValid())
end
o.restoreJumpPower = restore
o.uz = function() o.updateJumpPower(isCValid()) end
handleReady(UserInputService.JumpRequest:Connect(function()
if T.spoofJumpEnabled and not o.destroyed then pcall(o.uz) end
end))
end
local sz, Nz, ez
do
local leases = {}
local originals = setmetatable({}, { __mode = "k" })
local character
local function restore()
for part, collidable in pairs(originals) do
if part.Parent then pcall(function() part.CanCollide = collidable end) end
end
table.clear(originals)
end
local function wanted()
return T.noclipEnabled or T.noclipCarEnabled or T.vFlyEnabled or next(leases) ~= nil
end
local function step(force)
local current = LocalPlayer.Character
if character ~= current then restore(); character = current end
if o.destroyed or not current or (force ~= true and not wanted()) then restore(); return end
for _, part in ipairs(current:GetDescendants()) do
if part:IsA("BasePart") then
if originals[part] == nil then originals[part] = part.CanCollide end
if part.CanCollide then part.CanCollide = false end
end
end
end
o.noclip = {
acquire = function(owner) leases[owner] = true; step() end,
release = function(owner) leases[owner] = nil; step() end,
step = function() step() end,
restore = function() table.clear(leases); restore() end,
}
sz = function(force) step(force) end
Nz = function() step() end
ez = function() end
o.sz, o.Nz, o.ez = sz, Nz, ez
handleReady(LocalPlayer.CharacterAdded:Connect(function() step() end))
end
local Uz, Lz, kz = 60, 30, 14
local nz = Uz
local bz = Vector3.zero
local yz = false
local function xz(dt)
local root = isRoot()
local hum = isCValid()
if not root or not hum then return end
if not T.flyEnabled then
hum:SetStateEnabled(Enum.HumanoidStateType.Swimming, true)
hum:SetStateEnabled(Enum.HumanoidStateType.Jumping, true)
if hum:GetState() == Enum.HumanoidStateType.Swimming then
local target = hum.FloorMaterial == Enum.Material.Air
and Enum.HumanoidStateType.Freefall or Enum.HumanoidStateType.Running
pcall(hum.ChangeState, hum, target)
end
if T.autoFarmPescaEnabled or T.farmPartesEnabled then yz = false; return end
if isUpdateInstancePropertiesEValid() then
yz = false
root.AssemblyLinearVelocity = Vector3.zero
return
end
local _, vel, phys, _, stable = handleQ(root, dt, kz, true)
if yz and (stable and bz.Magnitude > 0) then
root.AssemblyLinearVelocity = bz * vel
return
end
yz = false
root.AssemblyLinearVelocity = Vector3.zero
return
end
if hum.Health <= 0 then return end
local flySpeed = math.clamp(tonumber(T.flySpeed) or 60, 0, 100)
hum:SetStateEnabled(Enum.HumanoidStateType.Jumping, false)
hum:ChangeState(Enum.HumanoidStateType.Swimming)
local vert = 0
if UserInputService:IsKeyDown(Enum.KeyCode.Space) then vert = 1
elseif UserInputService:IsKeyDown(Enum.KeyCode.LeftControl) then vert = -1 end
local dir = hum.MoveDirection + Vector3.new(0, vert, 0)
if dir.Magnitude <= 0 then
if isUpdateInstancePropertiesEValid() then
root.AssemblyLinearVelocity = Vector3.zero
return
end
local _, vel, _, _, stable = handleQ(root, dt, kz, true)
if stable and bz.Magnitude > 0 then
root.AssemblyLinearVelocity = bz * vel
else
root.AssemblyLinearVelocity = Vector3.zero
end
return
end
dir = dir.Unit
bz = dir
yz = true
if isUpdateInstancePropertiesEValid() then
root.AssemblyLinearVelocity = dir * flySpeed
return
end
local cap = hum.MoveDirection.Magnitude > 0 and Uz or nz
local goal = math.min(flySpeed, cap)
local vel = math.min(goal, Lz)
local _, step, _, _, stable = handleQ(root, dt, vel, true)
local wanted = math.min(goal, stable and l or Uz)
local want2 = math.max(0, wanted - step)
if want2 > 0 then
root.CFrame = root.CFrame + dir * want2 * (dt or 0.016)
end
root.AssemblyLinearVelocity = dir * step
end
o.xz = xz
do
local HEIGHT = 10
local PLATFORM_OFFSET = 3.5
local WALK_ANIMATION_R15 = "rbxassetid://507777826"
local WALK_ANIMATION_R6 = "rbxassetid://180426354"
local isGhost = false
local fakeModel = nil
local platform = nil
local walkTrack = nil
local camLockConnection = nil
local ghostNoclipEnabled = false
local changedParts = {}
local targetY = 0
local ghostConnection = nil
local ghostNoclipLoop = nil
local overlapParams = OverlapParams.new()
overlapParams.FilterType = Enum.RaycastFilterType.Exclude
local function startGhostNoclip()
if ghostNoclipEnabled then return end
ghostNoclipEnabled = true
table.clear(changedParts)
ghostNoclipLoop = RunService.Stepped:Connect(function()
if not ghostNoclipEnabled then return end
local char = LocalPlayer.Character
if not char then return end
local root = char:FindFirstChild("HumanoidRootPart")
if not root then return end
overlapParams.FilterDescendantsInstances = { char, fakeModel, platform }
local parts = workspace:GetPartBoundsInBox(root.CFrame, Vector3.new(4, 6, 4), overlapParams)
for _, part in ipairs(parts) do
if part:IsA("BasePart") and part.Parent and part ~= platform and part.CanCollide then
if changedParts[part] == nil then changedParts[part] = true end
part.CanCollide = false
end
end
end)
end
local function stopGhostNoclip()
ghostNoclipEnabled = false
if ghostNoclipLoop then ghostNoclipLoop:Disconnect(); ghostNoclipLoop = nil end
for part in pairs(changedParts) do
if part and part.Parent and part ~= platform then part.CanCollide = true end
end
table.clear(changedParts)
end
local function setupFakeCharacter(model)
local humanoid = model:FindFirstChildOfClass("Humanoid")
if not humanoid then return false end
humanoid.AutoRotate = false
humanoid.PlatformStand = true
humanoid.DisplayDistanceType = Enum.HumanoidDisplayDistanceType.None
for _, state in ipairs({ Enum.HumanoidStateType.FallingDown, Enum.HumanoidStateType.Ragdoll,
Enum.HumanoidStateType.Jumping, Enum.HumanoidStateType.Climbing,
Enum.HumanoidStateType.Swimming, Enum.HumanoidStateType.Freefall,
Enum.HumanoidStateType.Flying }) do
pcall(function() humanoid:SetStateEnabled(state, false) end)
end
for _, obj in ipairs(model:GetDescendants()) do
if obj:IsA("BasePart") then
obj.Anchored = true
obj.CanCollide = false
obj.CanTouch = false
obj.CanQuery = false
obj.Massless = true
elseif obj:IsA("Script") or obj:IsA("LocalScript") then
obj:Destroy()
end
end
local animator = humanoid:FindFirstChildOfClass("Animator") or Instance.new("Animator", humanoid)
local animation = Instance.new("Animation")
animation.Name = "VeteranoGhostWalk"
animation.AnimationId = (humanoid.RigType == Enum.HumanoidRigType.R15) and WALK_ANIMATION_R15 or WALK_ANIMATION_R6
local success, track = pcall(function() return animator:LoadAnimation(animation) end)
if success and track then
walkTrack = track
walkTrack.Looped = true
walkTrack.Priority = Enum.AnimationPriority.Movement
end
return true
end
local function updateFakeAnimation(realHumanoid)
if not walkTrack then return end
local moving = realHumanoid.MoveDirection.Magnitude > 0.05
if moving then
if not walkTrack.IsPlaying then walkTrack:Play(0.12) end
walkTrack:AdjustSpeed(math.clamp(realHumanoid.WalkSpeed / 16, 0.5, 2))
else
if walkTrack.IsPlaying then walkTrack:Stop(0.12) end
end
end
local function updateCameraAndGhost()
if not isGhost or not fakeModel or not LocalPlayer.Character then return end
local realChar = LocalPlayer.Character
local realRoot = realChar:FindFirstChild("HumanoidRootPart")
if not realRoot or not platform then return end
local fakeHumanoid = fakeModel:FindFirstChildOfClass("Humanoid")
if fakeHumanoid and workspace.CurrentCamera.CameraSubject ~= fakeHumanoid then
workspace.CurrentCamera.CameraSubject = fakeHumanoid
end
local currentPos = realRoot.Position
realRoot.AssemblyLinearVelocity = Vector3.new(
realRoot.AssemblyLinearVelocity.X, 0, realRoot.AssemblyLinearVelocity.Z)
platform.CFrame = CFrame.new(currentPos.X, targetY - PLATFORM_OFFSET, currentPos.Z)
fakeModel:PivotTo(CFrame.new(currentPos.X, targetY + HEIGHT, currentPos.Z) * realRoot.CFrame.Rotation)
local realHumanoid = realChar:FindFirstChildOfClass("Humanoid")
if realHumanoid then updateFakeAnimation(realHumanoid) end
end
local function stopGhost()
if not isGhost then return end
isGhost = false
RunService:UnbindFromRenderStep("VeteranoGhostCameraFix")
if camLockConnection then camLockConnection:Disconnect(); camLockConnection = nil end
stopGhostNoclip()
if walkTrack then pcall(function() walkTrack:Stop(0.1) end); walkTrack = nil end
local realChar = LocalPlayer.Character
local realRoot = realChar and realChar:FindFirstChild("HumanoidRootPart")
local realHumanoid = realChar and realChar:FindFirstChildOfClass("Humanoid")
if realHumanoid then realHumanoid:SetStateEnabled(Enum.HumanoidStateType.Jumping, true) end
if realRoot and fakeModel then
local fakeRoot = fakeModel:FindFirstChild("HumanoidRootPart")
if fakeRoot then realRoot.CFrame = fakeRoot.CFrame end
end
if realChar then
for _, obj in ipairs(realChar:GetDescendants()) do
if obj:IsA("BasePart") then
obj.Transparency = 0
obj.CanTouch = true
obj.CanQuery = true
obj.CanCollide = true
end
end
if realHumanoid then workspace.CurrentCamera.CameraSubject = realHumanoid end
end
if fakeModel then fakeModel:Destroy(); fakeModel = nil end
if platform then platform:Destroy(); platform = nil end
end
local function startGhost()
local realChar = LocalPlayer.Character
if not realChar then return end
local realRoot = realChar:FindFirstChild("HumanoidRootPart")
local realHumanoid = realChar:FindFirstChildOfClass("Humanoid")
if not realRoot or not realHumanoid then return end
isGhost = true
realHumanoid:SetStateEnabled(Enum.HumanoidStateType.Jumping, false)
local startCFrame = realRoot.CFrame
targetY = startCFrame.Y - HEIGHT
platform = Instance.new("Part")
platform.Name = "VeteranoGhostPlatform"
platform.Size = Vector3.new(10, 1, 10)
platform.Anchored = true
platform.CanCollide = true
platform.CanTouch = false
platform.CanQuery = false
platform.Transparency = 1
platform.Parent = workspace
realChar.Archivable = true
for _, desc in ipairs(realChar:GetDescendants()) do desc.Archivable = true end
local success, clonedChar = pcall(function() return realChar:Clone() end)
if not success or not clonedChar then stopGhost(); return end
fakeModel = clonedChar
fakeModel.Name = "VeteranoGhostVisual"
for _, obj in ipairs(fakeModel:GetDescendants()) do
if obj:IsA("Script") or obj:IsA("LocalScript") then obj:Destroy() end
end
fakeModel.Parent = workspace
local fakeRoot = fakeModel:FindFirstChild("HumanoidRootPart")
local fakeHumanoid = fakeModel:FindFirstChildOfClass("Humanoid")
if not fakeRoot or not fakeHumanoid then stopGhost(); return end
fakeModel.PrimaryPart = fakeRoot
if not setupFakeCharacter(fakeModel) then stopGhost(); return end
fakeModel:PivotTo(startCFrame)
for _, obj in ipairs(realChar:GetDescendants()) do
if obj:IsA("BasePart") then obj.Transparency = 1 end
end
startGhostNoclip()
realRoot.CFrame = CFrame.new(startCFrame.X, targetY, startCFrame.Z) * startCFrame.Rotation
workspace.CurrentCamera.CameraSubject = fakeHumanoid
camLockConnection = workspace.CurrentCamera:GetPropertyChangedSignal("CameraSubject"):Connect(function()
if isGhost and fakeHumanoid and workspace.CurrentCamera.CameraSubject ~= fakeHumanoid then
workspace.CurrentCamera.CameraSubject = fakeHumanoid
end
end)
RunService:BindToRenderStep("VeteranoGhostCameraFix", Enum.RenderPriority.Last.Value, updateCameraAndGhost)
end
o.startGhost = startGhost
o.stopGhost = stopGhost
o.isGhostActive = function() return isGhost end
handleReady(LocalPlayer.CharacterAdded:Connect(function()
if isGhost then stopGhost() end
end))
end
do
local NOME_FAKE = "@VETERANO.DEV"
local bindings = {}
local elapsed = 0
local function bind(obj, prop)
if not obj or bindings[obj] then return end
local ok, original = pcall(function() return obj[prop] end)
if not ok or type(original) ~= "string" then return end
local entry = { original = original, prop = prop, writing = false }
bindings[obj] = entry
local function enforce()
if entry.writing or not T.camuflarNickEnabled or o.destroyed or not obj.Parent then return end
local text = obj[prop]
if text == NOME_FAKE then return end
entry.original = text
entry.writing = true
obj[prop] = NOME_FAKE
entry.writing = false
end
entry.connection = obj:GetPropertyChangedSignal(prop):Connect(function() pcall(enforce) end)
enforce()
end
local function discover()
for obj, entry in pairs(bindings) do
if not obj.Parent then
entry.connection:Disconnect()
bindings[obj] = nil
end
end
local pg = LocalPlayer:FindFirstChild("PlayerGui")
local inventory = pg and pg:FindFirstChild("InventoryUI")
local frame = inventory and inventory:FindFirstChild("InvFrame")
local personal = frame and frame:FindFirstChild("InvPessoal")
local name = personal and personal:FindFirstChild("BTxt")
if name and (name:IsA("TextLabel") or name:IsA("TextButton") or name:IsA("TextBox")) then
bind(name, "Text")
end
local hum = isCValid()
if hum then bind(hum, "DisplayName") end
end
local function restore()
for obj, entry in pairs(bindings) do
entry.connection:Disconnect()
if obj.Parent then pcall(function() obj[entry.prop] = entry.original end) end
end
table.clear(bindings)
end
function o.setCamuflarNick(v)
T.camuflarNickEnabled = v == true
if T.camuflarNickEnabled and not o.destroyed then
discover()
else
restore()
end
end
handleReady(RunService.Heartbeat:Connect(function(dt)
if not T.camuflarNickEnabled or o.destroyed then return end
elapsed = elapsed + dt
if elapsed >= 0.35 then elapsed = 0; pcall(discover) end
end))
end
do
local acc = 0
local function autoCinto()
local st = LocalPlayer:FindFirstChild("Status")
local cinto = st and st:FindFirstChild("Cinto")
if not cinto or cinto.Value then return end
local char = LocalPlayer.Character
local hum = char and char:FindFirstChildOfClass("Humanoid")
local seat = hum and hum.SeatPart
if not seat or seat.Name ~= "DriveSeat" then return end
cinto.Value = true
pcall(function()
LocalPlayer.PlayerGui.OrpheusCarInterface.Velocimetro.Icons.Cinto.Visible = true
end)
pcall(function()
local sound = seat:FindFirstChild("ColocaCinto")
if sound then sound:Play() end
end)
end
handleReady(RunService.Heartbeat:Connect(function(dt)
if not T.autoCintoEnabled then return end
acc = acc + dt
if acc < 0.3 then return end
acc = 0
pcall(autoCinto)
end))
end
local function Dz(hum)
if type(getconnections) ~= "function" then return nil end
if type(debug) ~= "table" or type(debug.getupvalue) ~= "function" then return nil end
for _, conn in ipairs(getconnections(RunService.Stepped)) do
local fn = conn.Function
if type(fn) == "function" then
local hit, carState
for i = 1, 8 do
local ok, up = pcall(debug.getupvalue, fn, i)
if not ok then break end
if up == hum then hit = true end
if type(up) == "table" and up.RemapMultiplier ~= nil and type(up.BuildHPCache) == "function" then
carState = up
end
end
if hit and carState then return carState end
end
end
return nil
end
local function rz()
local hum = isCValid()
local seat = hum and hum.SeatPart
if not seat or seat.Name ~= "DriveSeat" then return nil end
local carros = workspace:FindFirstChild("CarrosSpawandos")
if not carros then return nil end
local p = seat
while p and p.Parent ~= carros do p = p.Parent end
return p
end
do
local carCfg = {
carro = nil, originais = {}, raiz = nil, assento = nil,
relativoAssento = nil, altura = nil, piso = nil, raycast = nil,
velocidade = 0, frente = nil, personagemAtivo = false,
altitude = nil, vFlyAtivo = false, vFlyReduzidoAte = 0,
}
local function findSeat(car)
local seat = car:FindFirstChild("DriveSeat", true) or car:FindFirstChildWhichIsA("VehicleSeat", true)
return (seat and seat.AssemblyRootPart) or car.PrimaryPart or seat, seat
end
local function restaurarNoclipCar()
for part, v in pairs(carCfg.originais) do
if part.Parent then pcall(function() part.CanCollide = v end) end
end
table.clear(carCfg.originais)
if carCfg.personagemAtivo and not T.noclipEnabled then pcall(sz, false) end
carCfg.carro, carCfg.raiz, carCfg.assento = nil, nil, nil
carCfg.relativoAssento, carCfg.altura, carCfg.piso, carCfg.raycast = nil, nil, nil, nil
carCfg.velocidade, carCfg.frente, carCfg.personagemAtivo = 0, nil, false
carCfg.altitude, carCfg.vFlyAtivo, carCfg.vFlyReduzidoAte = nil, false, 0
end
local function prepararNoclipCar(car)
local raiz, seat = findSeat(car)
if not raiz or not seat then return end
local rp = RaycastParams.new()
rp.FilterType = Enum.RaycastFilterType.Exclude
rp.FilterDescendantsInstances = { car, LocalPlayer.Character }
local hit = workspace:Raycast(raiz.Position + Vector3.new(0, 20, 0), Vector3.new(0, -120, 0), rp)
carCfg.raiz = raiz
carCfg.assento = seat
carCfg.relativoAssento = ((raiz.CFrame - raiz.Position)):ToObjectSpace(seat.CFrame - seat.Position)
carCfg.raycast = rp
carCfg.piso = hit and hit.Position.Y or (raiz.Position.Y - 3)
carCfg.altura = hit and math.max(1, raiz.Position.Y - hit.Position.Y) or 3
local fwd = Vector3.new(seat.CFrame.LookVector.X, 0, seat.CFrame.LookVector.Z)
carCfg.frente = fwd.Magnitude > 0 and fwd.Unit or Vector3.new(0, 0, -1)
carCfg.altitude = raiz.Position.Y
carCfg.velocidade = fwd.Magnitude > 0 and raiz.AssemblyLinearVelocity:Dot(fwd.Unit) or 0
end
local function max(a, b, d)
if a < b then return math.min(a + d, b) end
return math.max(a - d, b)
end
local function tickCar(dt)
local raiz, seat = carCfg.raiz, carCfg.assento
local rp, altura, piso = carCfg.raycast, carCfg.altura, carCfg.piso
local rel = carCfg.relativoAssento
if not raiz or not raiz.Parent or not seat or not seat.Parent or not rp or not altura or not piso or not rel then return end
local step = math.min(dt or 0.016666, 0.1)
local flying = T.vFlyEnabled
local unstable = false
if flying and not carCfg.vFlyAtivo then
carCfg.altitude = raiz.Position.Y
carCfg.vFlyReduzidoAte = os.clock() + 0.6
carCfg.velocidade = math.clamp(carCfg.velocidade, -24, 36)
elseif not flying and carCfg.vFlyAtivo then
local hit = workspace:Raycast(raiz.Position + Vector3.new(0, 20, 0), Vector3.new(0, -520, 0), rp)
if hit then
piso = hit.Position.Y
carCfg.piso = piso
end
end
carCfg.vFlyAtivo = flying
local vertAdj = 0
if flying then
if step >= 0.05 or raiz.Anchored or raiz.AssemblyLinearVelocity.Magnitude < 12 then
carCfg.vFlyReduzidoAte = os.clock() + 0.6
end
else
local scanStart = Vector3.new(raiz.Position.X, piso + 20, raiz.Position.Z)
local hit = workspace:Raycast(scanStart, Vector3.new(0, -60, 0), rp)
if hit and hit.Position.Y - piso <= 4 then
piso = hit.Position.Y
carCfg.piso = piso
end
vertAdj = (piso + altura) - raiz.Position.Y
unstable = math.abs(vertAdj) > 1
if unstable then vertAdj = math.clamp(vertAdj, -20 * step, 20 * step) end
end
local fwd = carCfg.frente or Vector3.new(0, 0, -1)
local verticalVel = 0
if fwd.Magnitude > 0.001 then
fwd = fwd.Unit
local throttle = seat.ThrottleFloat
local steer = seat.SteerFloat
if UserInputService:IsKeyDown(Enum.KeyCode.W) or UserInputService:IsKeyDown(Enum.KeyCode.Up) then throttle = 1
elseif UserInputService:IsKeyDown(Enum.KeyCode.S) or UserInputService:IsKeyDown(Enum.KeyCode.Down) then throttle = -1 end
if UserInputService:IsKeyDown(Enum.KeyCode.A) or UserInputService:IsKeyDown(Enum.KeyCode.Left) then steer = -1
elseif UserInputService:IsKeyDown(Enum.KeyCode.D) or UserInputService:IsKeyDown(Enum.KeyCode.Right) then steer = 1 end
local unblocked = isUpdateInstancePropertiesEValid()
local boost = math.clamp(T.boostFactor, 1, 999)
local vMax
if flying then vMax = T.vFlyHorizontalSpeed * boost
else vMax = unstable and 260 or (unblocked and 999 or 180) * boost end
local vMin = flying and (vMax * 0.65) or (unstable and 120 or (150 * boost))
local target = throttle > 0.05 and vMax or (throttle < -0.05 and -vMin or 0)
local accel = (math.abs(throttle) > 0.05 and 130 or 200) * step
carCfg.velocidade = max(carCfg.velocidade, target, accel)
if math.abs(steer) > 0.05 and math.abs(carCfg.velocidade) > 0.5 then
local sign = carCfg.velocidade >= 0 and 1 or -1
local ang = ((-steer * sign) * math.rad(85)) * step
fwd = CFrame.Angles(0, ang, 0):VectorToWorldSpace(fwd)
end
carCfg.frente = fwd.Unit
if flying then
local vInput = 0
if UserInputService:IsKeyDown(Enum.KeyCode.Space) then vInput = 1
elseif UserInputService:IsKeyDown(Enum.KeyCode.LeftControl) then vInput = -1 end
local vCap = T.vFlyVerticalSpeed * boost
if vInput ~= 0 then
verticalVel = vInput * vCap
carCfg.altitude = raiz.Position.Y + verticalVel * step
else
local diff = (carCfg.altitude or raiz.Position.Y) - raiz.Position.Y
verticalVel = math.clamp(diff * 10, -vCap, vCap)
end
end
local look = CFrame.lookAt(Vector3.zero, fwd.Unit, Vector3.yAxis)
local rot = look * rel:Inverse()
if flying then
raiz.CFrame = CFrame.new(raiz.Position + Vector3.new(0, vertAdj, 0)) * rot
elseif math.abs(vertAdj) > 0.01 then
raiz.CFrame = raiz.CFrame + Vector3.new(0, vertAdj, 0)
end
if fwd.Magnitude > 0.001 then
raiz.AssemblyLinearVelocity = fwd.Unit * carCfg.velocidade + Vector3.new(0, verticalVel, 0)
else
raiz.AssemblyLinearVelocity = Vector3.new(0, verticalVel, 0)
end
raiz.AssemblyAngularVelocity = Vector3.zero
end
end
local function aplicarNoclipCar()
if not T.noclipCarEnabled and not T.vFlyEnabled then
restaurarNoclipCar()
return
end
local car = rz()
if car ~= carCfg.carro then
restaurarNoclipCar()
carCfg.carro = car
if car then prepararNoclipCar(car) end
end
if not car then return end
sz(true)
carCfg.personagemAtivo = true
for _, p in ipairs(car:GetDescendants()) do
if p:IsA("BasePart") then
if carCfg.originais[p] == nil then carCfg.originais[p] = p.CanCollide end
if p.CanCollide then p.CanCollide = false end
end
end
end
o.aplicarNoclipCar = aplicarNoclipCar
o.restaurarNoclipCar = restaurarNoclipCar
handleReady(RunService.Stepped:Connect(function()
if not o.destroyed then pcall(aplicarNoclipCar) end
end))
handleReady(RunService.Heartbeat:Connect(function(dt)
if not o.destroyed and (T.noclipCarEnabled or T.vFlyEnabled) then pcall(tickCar, dt) end
end))
end
local function hz(hum)
local st = Dz(hum)
if not st then return false end
st.RemapMultiplier = math.clamp(T.boostFactor, 1, 999)
pcall(function() st:BuildHPCache() end)
pcall(function()
if st.SpeedLimit ~= nil then st.SpeedLimit = 999 end
if st.MaxSpeed ~= nil then st.MaxSpeed = 999 end
if st.TopSpeed ~= nil then st.TopSpeed = 999 end
if st.SpeedCap ~= nil then st.SpeedCap = 999 end
end)
return true
end
o.hz = hz
task.spawn(function()
local lastCar
while not o.destroyed do
local car = rz()
if car then o.lastDrivenCar = car end
if car and car ~= lastCar then
local hum = isCValid()
if hum then hz(hum) end
lastCar = car
elseif not car then
lastCar = nil
end
task.wait(0.5)
end
end)
do
local MAX = 1500
local green = Color3.fromRGB(70, 190, 120)
local yellow = Color3.fromRGB(232, 190, 70)
local red = Color3.fromRGB(232, 92, 84)
local bg = Color3.fromRGB(22, 14, 34)
local border = Color3.fromRGB(100, 60, 160)
local track = Color3.fromRGB(40, 26, 60)
local textCol = Color3.fromRGB(240, 230, 255)
local muted = Color3.fromRGB(170, 140, 210)
local fontName = "rbxasset://fonts/families/Nunito.json"
local boldFont = Font.new(fontName, Enum.FontWeight.Bold, Enum.FontStyle.Normal)
local regFont = Font.new(fontName, Enum.FontWeight.Regular, Enum.FontStyle.Normal)
local W, Hh = 208, 64
local O = TweenInfo.new(0.25, Enum.EasingStyle.Quart, Enum.EasingDirection.Out)
local S_ = {
gui = nil, frame = nil, fill = nil, percentLabel = nil, valueLabel = nil,
carLabel = nil, uiScale = nil, vidaConn = nil, car = nil,
maxVida = MAX, savedPosition = nil, painel = nil,
}
o.durHUD = S_
local function mk(kind, props, parent)
local obj = Instance.new(kind)
for k, v in pairs(props) do obj[k] = v end
obj.Parent = parent
return obj
end
local function getParent() return LocalPlayer:WaitForChild("PlayerGui") end
local function colorFor(pct)
if pct <= 20 then return red end
if pct <= 50 then return yellow end
return green
end
local function vpSize()
local cam = workspace.CurrentCamera
return cam and cam.ViewportSize or Vector2.new(800, 600)
end
local function updateScale()
if not S_.uiScale then return end
local vp = vpSize()
S_.uiScale.Scale = math.clamp(vp.X / 1280, 0.85, 1.1)
end
local function defaultPos()
local vp = vpSize()
local yy = math.max(40, (vp.Y - Hh) - 150)
return UDim2.fromOffset(14, yy)
end
local function setVal(v)
if not S_.frame then return end
if v > S_.maxVida then S_.maxVida = v end
local pct = math.clamp((v / S_.maxVida) * 100, 0, 100)
local col = colorFor(pct)
S_.percentLabel.Text = string.format("%d%%", math.floor(pct + 0.5))
S_.percentLabel.TextColor3 = col
S_.valueLabel.Text = string.format("%d / %d", math.floor(v + 0.5), math.floor(S_.maxVida + 0.5))
TweenService:Create(S_.fill, O, { Size = UDim2.new(pct / 100, 0, 1, 0), BackgroundColor3 = col }):Play()
end
local function create()
if S_.gui and S_.gui.Parent then return end
local gui = mk("ScreenGui", {
Name = "VeteranoDurabilidade", ResetOnSpawn = false,
IgnoreGuiInset = true, DisplayOrder = 9999,
ZIndexBehavior = Enum.ZIndexBehavior.Sibling,
}, getParent())
local frame = mk("Frame", {
Name = "HUD", Active = true, AnchorPoint = Vector2.new(0, 0),
Position = S_.savedPosition or defaultPos(),
Size = UDim2.fromOffset(W, Hh),
BackgroundColor3 = bg, BackgroundTransparency = 0.08,
BorderSizePixel = 0, Visible = false,
}, gui)
S_.uiScale = mk("UIScale", { Scale = 1 }, frame)
mk("UICorner", { CornerRadius = UDim.new(0, 10) }, frame)
mk("UIStroke", { Color = border, Transparency = 0.2, Thickness = 1, ApplyStrokeMode = Enum.ApplyStrokeMode.Border }, frame)
mk("UIPadding", {
PaddingTop = UDim.new(0, 9), PaddingBottom = UDim.new(0, 9),
PaddingLeft = UDim.new(0, 11), PaddingRight = UDim.new(0, 11),
}, frame)
S_.carLabel = mk("TextLabel", {
Name = "Carro", Position = UDim2.fromOffset(0, 0),
Size = UDim2.new(1, -46, 0, 15), BackgroundTransparency = 1,
Text = "Veiculo", TextColor3 = textCol, TextSize = 14,
FontFace = boldFont, TextXAlignment = Enum.TextXAlignment.Left,
TextTruncate = Enum.TextTruncate.AtEnd,
}, frame)
S_.percentLabel = mk("TextLabel", {
Name = "Percent", AnchorPoint = Vector2.new(1, 0),
Position = UDim2.new(1, 0, 0, 0), Size = UDim2.fromOffset(46, 15),
BackgroundTransparency = 1, Text = "--%", TextColor3 = green,
TextSize = 15, FontFace = boldFont,
TextXAlignment = Enum.TextXAlignment.Right,
}, frame)
local bar = mk("Frame", {
Name = "Barra", Position = UDim2.fromOffset(0, 24),
Size = UDim2.new(1, 0, 0, 9), BackgroundColor3 = track,
BorderSizePixel = 0, ClipsDescendants = true,
}, frame)
mk("UICorner", { CornerRadius = UDim.new(1, 0) }, bar)
S_.fill = mk("Frame", { Name = "Fill", Size = UDim2.new(1, 0, 1, 0), BackgroundColor3 = green, BorderSizePixel = 0 }, bar)
mk("UICorner", { CornerRadius = UDim.new(1, 0) }, S_.fill)
S_.valueLabel = mk("TextLabel", {
Name = "Valor", Position = UDim2.fromOffset(0, 37),
Size = UDim2.new(1, 0, 0, 12), BackgroundTransparency = 1,
Text = "-- / --", TextColor3 = muted, TextSize = 12,
FontFace = regFont, TextXAlignment = Enum.TextXAlignment.Left,
}, frame)
S_.gui = gui
S_.frame = frame
updateScale()
handleReady((workspace:GetPropertyChangedSignal("CurrentCamera")):Connect(function()
updateScale()
local cam = workspace.CurrentCamera
if cam then handleReady((cam:GetPropertyChangedSignal("ViewportSize")):Connect(updateScale)) end
end))
local cam = workspace.CurrentCamera
if cam then handleReady((cam:GetPropertyChangedSignal("ViewportSize")):Connect(updateScale)) end
end
o.vidaDoCarro = function(car)
local body = car:FindFirstChild("Body")
local paint = body and body:FindFirstChild("Pintura")
local life = paint and paint:FindFirstChild("LifeController")
return life and life:FindFirstChild("Vida")
end
local function getMeuCarro()
local hum = isCValid()
local seat = hum and hum.SeatPart
if not seat then return nil end
local carros = workspace:FindFirstChild("CarrosSpawandos")
if not carros then return nil end
local p = seat
while p and p.Parent do
if p.Parent == carros then return p end
p = p.Parent
end
return nil
end
local function desconectar()
if S_.vidaConn then S_.vidaConn:Disconnect(); S_.vidaConn = nil end
S_.car = nil
if S_.painel then S_.painel:SetVisible(false) end
end
local function conectar(car)
local vida = o.vidaDoCarro(car)
if not vida then return end
if S_.vidaConn then S_.vidaConn:Disconnect() end
S_.car = car
S_.maxVida = math.max(MAX, vida.Value)
if S_.carLabel then S_.carLabel.Text = car.Name end
if S_.painel then S_.painel:SetVisible(true) end
setVal(vida.Value)
S_.vidaConn = vida:GetPropertyChangedSignal("Value"):Connect(function() setVal(vida.Value) end)
end
function o.ativarDurabilidade()
create()
S_.frame.Visible = true
o.durabilidadeRunId = o.durabilidadeRunId + 1
local runId = o.durabilidadeRunId
task.spawn(function()
while T.durabilidadeHudEnabled and not o.destroyed and o.durabilidadeRunId == runId do
local car = getMeuCarro()
if car ~= S_.car then
if car and o.vidaDoCarro(car) then pcall(conectar, car)
else pcall(desconectar); if S_.frame then S_.frame.Visible = false end end
elseif car and not S_.vidaConn then
pcall(conectar, car)
end
if S_.frame then S_.frame.Visible = car ~= nil end
task.wait(0.3)
end
end)
end
function o.desativarDurabilidade()
o.durabilidadeRunId = o.durabilidadeRunId + 1
pcall(desconectar)
if S_.frame then S_.frame.Visible = false end
end
function o.destruirDurabilidade()
pcall(desconectar)
if S_.gui then pcall(function() S_.gui:Destroy() end) end
S_.gui, S_.frame, S_.fill = nil, nil, nil
end
end
task.spawn(function()
while not o.destroyed do
if T.monitorarStaff then
local count, nomes = 0, {}
for _, plr in ipairs(Players:GetPlayers()) do
if plr ~= LocalPlayer and Jz.Admin(plr) then
count += 1
table.insert(nomes, plr.Name)
end
end
o.staffCount = count
o.staffNames = nomes
end
task.wait(1)
end
end)
local wz = { medido = false, drawing = false, texto = false }
function wz.set(obj, prop, val) return pcall(function() obj[prop] = val end) end
function wz.medir()
if wz.medido then return wz end
if Drawing == nil or type(Drawing.new) ~= "function" then return wz end
local ok, sq = pcall(Drawing.new, "Square")
if not ok then return wz end
wz.drawing = true
wz.filled = wz.set(sq, "Filled", false)
wz.thickness = wz.set(sq, "Thickness", 1)
pcall(function() sq:Remove() end)
local ok2, txt = pcall(Drawing.new, "Text")
if ok2 then
wz.texto = true
wz.center = wz.set(txt, "Center", true)
wz.outline = wz.set(txt, "Outline", true)
wz.outlineColor = wz.set(txt, "OutlineColor", Color3.new())
wz.font = wz.set(txt, "Font", 2)
pcall(function() txt.Text = "MMM" end)
local ok3, tb = pcall(function() return txt.TextBounds end)
wz.textBounds = ok3 and (typeof(tb) == "Vector2" and tb.X > 0)
pcall(function() txt:Remove() end)
end
local ok4, tr = pcall(Drawing.new, "Triangle")
wz.triangle = ok4 == true
if ok4 then pcall(function() tr:Remove() end) end
wz.medido = true
return wz
end
function wz.ok() return (wz.medir()).drawing end
local function az(kind)
if not wz.ok() then return nil end
if kind == "Triangle" and not wz.triangle then return nil end
local ok, obj = pcall(function() return Drawing.new(kind) end)
if not ok then return nil end
wz.set(obj, "Visible", false)
wz.set(obj, "Color", Color3.fromRGB(170, 70, 255))
wz.set(obj, "Transparency", 1)
wz.set(obj, "Thickness", 1)
return obj
end
local function iz(col)
if not wz.ok() or not wz.texto then return nil end
local ok, obj = pcall(function() return Drawing.new("Text") end)
if not ok then return nil end
wz.set(obj, "Visible", false)
wz.set(obj, "Color", col or Color3.fromRGB(170, 70, 255))
wz.set(obj, "Transparency", 1)
wz.set(obj, "Size", 13)
if wz.center then wz.set(obj, "Center", true) end
if wz.outline then wz.set(obj, "Outline", true) end
if wz.outlineColor then wz.set(obj, "OutlineColor", Color3.new(0, 0, 0)) end
if wz.font then wz.set(obj, "Font", 2) end
return obj
end
local function larguraText(obj)
if not obj then return 0 end
if wz.textBounds then
local ok, tb = pcall(function() return obj.TextBounds end)
if ok and tb then return tb.X end
end
local ok, txt = pcall(function() return obj.Text end)
if not ok or type(txt) ~= "string" then return 0 end
local ok2, sz = pcall(function() return obj.Size end)
return (#txt * (ok2 and sz or 13)) * 0.52
end
local dz = {
{ "Head", "UpperTorso" }, { "UpperTorso", "LowerTorso" },
{ "UpperTorso", "LeftUpperArm" }, { "LeftUpperArm", "LeftLowerArm" }, { "LeftLowerArm", "LeftHand" },
{ "UpperTorso", "RightUpperArm" }, { "RightUpperArm", "RightLowerArm" }, { "RightLowerArm", "RightHand" },
{ "LowerTorso", "LeftUpperLeg" }, { "LeftUpperLeg", "LeftLowerLeg" }, { "LeftLowerLeg", "LeftFoot" },
{ "LowerTorso", "RightUpperLeg" }, { "RightUpperLeg", "RightLowerLeg" }, { "RightLowerLeg", "RightFoot" },
}
local Gz = {
{ "Head", "Torso" }, { "Torso", "Left Arm" }, { "Torso", "Right Arm" },
{ "Torso", "Left Leg" }, { "Torso", "Right Leg" },
}
local tz = #dz
local Kz = { { 1, 2 }, { 1, 3 }, { 1, 5 }, { 2, 4 }, { 2, 6 }, { 3, 4 }, { 3, 7 }, { 4, 8 }, { 5, 6 }, { 5, 7 }, { 6, 8 }, { 7, 8 } }
local CF, YF, gF, mF = 300, 0.016666, 0.033333, 0.05
local EF, MF = 4, 5
local HF = function(prev, now, interval) if prev <= 0 or now - prev > interval then return now + interval end return prev + interval end
local function AF(plr)
local b2 = {
player = plr, box = az("Square"),
name = iz(Color3.fromRGB(170, 70, 255)),
distance = iz(Color3.fromRGB(190, 200, 225)),
item = iz(Color3.fromRGB(190, 200, 225)),
team = iz(Color3.fromRGB(170, 70, 255)),
lines = {}, tracer = nil, arrow = nil, box3dLines = {}, highlight = nil,
character = nil, humanoid = nil, root = nil,
bodyParts = {}, skeletonPairs = {},
relationshipColor = nil, relationshipLabel = "",
nextVisualUpdate = 0, nextSkeletonUpdate = 0,
}
if b2.box and wz.filled then b2.box.Filled = false end
for _ = 1, tz do table.insert(b2.lines, az("Line")) end
T.bundles[plr] = b2
return b2
end
local function FF(b2)
if b2.box then b2.box.Visible = false end
if b2.name then b2.name.Visible = false end
if b2.distance then b2.distance.Visible = false end
if b2.item then b2.item.Visible = false end
if b2.team then b2.team.Visible = false end
if b2.tracer then b2.tracer.Visible = false end
if b2.arrow then b2.arrow.Visible = false end
for _, l in ipairs(b2.lines) do l.Visible = false end
for _, l in ipairs(b2.box3dLines) do l.Visible = false end
end
local function uF(b2)
FF(b2)
if b2.highlight then b2.highlight.Enabled = false end
end
local function qF(b2)
if not b2 then return end
local function h(obj) if obj then pcall(function() obj:Remove() end) end end
h(b2.box); h(b2.name); h(b2.distance); h(b2.item); h(b2.team)
h(b2.tracer); h(b2.arrow)
for _, l in ipairs(b2.lines) do h(l) end
for _, l in ipairs(b2.box3dLines) do h(l) end
if b2.highlight then pcall(function() b2.highlight:Destroy() end); b2.highlight = nil end
end
o.qF = qF
local function PF_all()
for _, b2 in pairs(T.bundles) do uF(b2) end
end
o.PF = PF_all
local function zF(cam, pos)
local s, onScreen = cam:WorldToViewportPoint(pos)
return Vector2.new(s.X, s.Y), onScreen, s.Z
end
local function oF(b2)
if b2.tracer then return b2.tracer end
b2.tracer = az("Line")
if b2.tracer then
if wz.thickness then b2.tracer.Thickness = 1.5 end
b2.tracer.Color = b2.relationshipColor or Color3.fromRGB(170, 70, 255)
end
return b2.tracer
end
local function TF(b2)
if b2.arrow then return b2.arrow end
b2.arrow = az("Triangle")
if b2.arrow then
if wz.filled then b2.arrow.Filled = true end
if wz.thickness then b2.arrow.Thickness = 1 end
b2.arrow.Color = b2.relationshipColor or Color3.fromRGB(170, 70, 255)
end
return b2.arrow
end
local function fF(b2)
if #b2.box3dLines > 0 then return b2.box3dLines end
for _ = 1, #Kz do
local l = az("Line")
if l then
if wz.thickness then l.Thickness = 1 end
l.Color = b2.relationshipColor or Color3.fromRGB(170, 70, 255)
table.insert(b2.box3dLines, l)
end
end
return b2.box3dLines
end
local function RF(b2, char)
local hl = b2.highlight
if not hl or not hl.Parent then
local ok, newHl = pcall(function()
local h = Instance.new("Highlight")
h.Name = "VeteranoESP_" .. tostring(b2.player.UserId)
h.DepthMode = Enum.HighlightDepthMode.AlwaysOnTop
h.FillTransparency = 0.78
h.OutlineTransparency = 0
h.Parent = workspace.CurrentCamera or workspace
return h
end)
if not ok then return nil end
hl = newHl
b2.highlight = hl
elseif hl.Parent ~= (workspace.CurrentCamera or workspace) then
hl.Parent = workspace.CurrentCamera or workspace
end
local col = b2.relationshipColor or Color3.fromRGB(170, 70, 255)
hl.Adornee = char
hl.FillColor = col
hl.OutlineColor = col
return hl
end
local function QF(b2, cam, screenPos, z)
local arrow = TF(b2)
if not arrow then return end
local vp = cam.ViewportSize
local center = Vector2.new(vp.X * 0.5, vp.Y * 0.5)
local dir = screenPos - center
if z <= 0 then dir = -dir end
if dir.Magnitude < 0.001 then dir = Vector2.new(0, -1) end
local unit = dir.Unit
local radius = math.max(40, math.min(vp.X, vp.Y) * 0.42)
local tip = center + unit * radius
local back = tip - unit * 14
local perp = Vector2.new(-unit.Y, unit.X) * 8
arrow.PointA = tip
arrow.PointB = back + perp
arrow.PointC = back - perp
arrow.Visible = true
end
local function VF(b2, char, hum, root)
if b2.character == char and b2.humanoid == hum and b2.root == root then return end
b2.character, b2.humanoid, b2.root = char, hum, root
table.clear(b2.bodyParts)
table.clear(b2.skeletonPairs)
for _, p in ipairs(char:GetChildren()) do
if p:IsA("BasePart") then table.insert(b2.bodyParts, p) end
end
local pairsList = (hum.RigType == Enum.HumanoidRigType.R15) and dz or Gz
for _, pr in ipairs(pairsList) do
table.insert(b2.skeletonPairs, { char:FindFirstChild(pr[1]), char:FindFirstChild(pr[2]) })
end
end
local function pF(plr)
local t = plr.Team
local name = t and t.Name or nil
if Jz.Admin(plr) and name ~= "STAFF" then
return name and ("STAFF | " .. name) or "STAFF"
end
return name
end
local function IF(plr)
local t = plr.Team
local ok, col = pcall(function() return t.TeamColor.Color end)
return (ok and col) or Color3.fromRGB(170, 70, 255)
end
local function OF(plr, b2)
local col, suffix = Jz.Appearance(plr)
b2.relationshipLabel = suffix
if b2.relationshipColor == col then return end
b2.relationshipColor = col
if b2.box then b2.box.Color = col end
if b2.name then b2.name.Color = col end
if b2.distance then b2.distance.Color = col end
if b2.item then b2.item.Color = col end
if b2.tracer then b2.tracer.Color = col end
if b2.arrow then b2.arrow.Color = col end
if b2.highlight then
b2.highlight.FillColor = col
b2.highlight.OutlineColor = col
end
for _, l in ipairs(b2.lines) do if l then l.Color = col end end
for _, l in ipairs(b2.box3dLines) do if l then l.Color = col end end
end
local function XF(plr, b2, cam, myPos, now)
local char = plr.Character
local hum = char and char:FindFirstChildOfClass("Humanoid")
local root = char and char:FindFirstChild("HumanoidRootPart")
if not char or not hum or hum.Health <= 0 or not root then
b2.character = nil
b2.nextVisualUpdate = now + mF
uF(b2)
return
end
VF(b2, char, hum, root)
local dist = (root.Position - myPos).Magnitude
if dist > T.maxDistance then
b2.nextVisualUpdate = now + mF
uF(b2)
return
end
OF(plr, b2)
if T.highlight then
local hl = RF(b2, char)
if hl then hl.Enabled = true end
elseif b2.highlight then b2.highlight.Enabled = false end
local screenPos, onScreen, z = zF(cam, root.Position)
if not onScreen or z <= 0 then
b2.nextVisualUpdate = now + mF
FF(b2)
if T.arrow then QF(b2, cam, screenPos, z) end
return
end
local close = dist <= CF
local interval = close and YF or gF
b2.nextVisualUpdate = HF(b2.nextVisualUpdate, now, interval)
if b2.arrow then b2.arrow.Visible = false end
local minX, minY, maxX, maxY = math.huge, math.huge, -math.huge, -math.huge
local anyOnScreen = false
local screenCorners, zCorners = {}, {}
if T.box or T.name or T.distance or T.box3d or T.teamTag then
local bbMin, bbMax
for _, p in ipairs(b2.bodyParts) do
if p.Parent == char then
local half = p.Size * 0.5
local a, b2v = p.Position - half, p.Position + half
if bbMin then
bbMin = Vector3.new(math.min(bbMin.X, a.X), math.min(bbMin.Y, a.Y), math.min(bbMin.Z, a.Z))
bbMax = Vector3.new(math.max(bbMax.X, b2v.X), math.max(bbMax.Y, b2v.Y), math.max(bbMax.Z, b2v.Z))
else
bbMin, bbMax = a, b2v
end
end
end
if bbMin then
local idx = 0
for xi = 0, 1 do for yi = 0, 1 do for zi = 0, 1 do
idx = idx + 1
local corner = Vector3.new(xi == 0 and bbMin.X or bbMax.X, yi == 0 and bbMin.Y or bbMax.Y, zi == 0 and bbMin.Z or bbMax.Z)
local sp, onS, zc = zF(cam, corner)
screenCorners[idx] = sp
zCorners[idx] = zc
if zc > 0.5 then
anyOnScreen = true
minX = math.min(minX, sp.X); minY = math.min(minY, sp.Y)
maxX = math.max(maxX, sp.X); maxY = math.max(maxY, sp.Y)
end
end end end
end
if anyOnScreen then
local vp = cam.ViewportSize
minX = math.clamp(minX, -32, vp.X + 32)
maxX = math.clamp(maxX, -32, vp.X + 32)
minY = math.clamp(minY, -32, vp.Y + 32)
maxY = math.clamp(maxY, -32, vp.Y + 32)
if maxX - minX < 1 or maxY - minY < 1 then anyOnScreen = false end
end
end
if T.box and b2.box then
if anyOnScreen then
b2.box.Position = Vector2.new(minX, minY)
b2.box.Size = Vector2.new(maxX - minX, maxY - minY)
b2.box.Visible = true
else b2.box.Visible = false end
elseif b2.box then b2.box.Visible = false end
if T.name and b2.name then
if anyOnScreen then
b2.name.Text = plr.Name .. b2.relationshipLabel
b2.name.Position = Vector2.new((minX + maxX) / 2, minY - 18)
b2.name.Visible = true
else b2.name.Visible = false end
elseif b2.name then b2.name.Visible = false end
if T.teamTag and b2.team then
local teamName = anyOnScreen and pF(plr) or nil
if teamName then
b2.team.Text = teamName
if Jz.Admin(plr) and plr.Team and plr.Team.Name == "STAFF" then
b2.team.Color = Color3.fromRGB(255, 200, 50)
else
b2.team.Color = IF(plr)
end
b2.team.Position = Vector2.new((minX + maxX) / 2, (T.name and (b2.name and b2.name.Visible)) and (minY - 32) or (minY - 18))
b2.team.Visible = true
else b2.team.Visible = false end
elseif b2.team then b2.team.Visible = false end
if T.distance and b2.distance then
if anyOnScreen then
b2.distance.Text = string.format("%dm", math.floor(dist))
if wz.center then b2.distance.Center = true end
b2.distance.Position = Vector2.new((minX + maxX) / 2, maxY + 3)
b2.distance.Visible = true
else b2.distance.Visible = false end
elseif b2.distance then b2.distance.Visible = false end
if T.heldItem and b2.item then
local c2 = b2.character or plr.Character
local tool = anyOnScreen and (c2 and c2:FindFirstChildOfClass("Tool"))
if tool then
b2.item.Text = tool.Name
local cx = (minX + maxX) / 2
if T.distance and (b2.distance and b2.distance.Visible) then
local dw = larguraText(b2.distance)
local iw = larguraText(b2.item)
local startX = cx - ((dw + MF) + iw) / 2
if wz.center then b2.distance.Center = false end
b2.distance.Position = Vector2.new(startX, maxY + 3)
if wz.center then b2.item.Center = false end
b2.item.Position = Vector2.new((startX + dw) + MF, maxY + 3)
else
if wz.center then b2.item.Center = true end
b2.item.Position = Vector2.new(cx, maxY + 3)
end
b2.item.Visible = true
else b2.item.Visible = false end
elseif b2.item then b2.item.Visible = false end
if T.tracer then
local tr = oF(b2)
if tr then
local vp = cam.ViewportSize
tr.From = Vector2.new(vp.X * 0.5, 4)
tr.To = anyOnScreen and Vector2.new((minX + maxX) * 0.5, (minY + maxY) * 0.5) or screenPos
tr.Visible = true
end
elseif b2.tracer then b2.tracer.Visible = false end
if T.box3d then
local lines = fF(b2)
for i, pr in ipairs(Kz) do
local l = lines[i]
local p1, p2 = screenCorners[pr[1]], screenCorners[pr[2]]
local z1, z2 = zCorners[pr[1]], zCorners[pr[2]]
if l and p1 and p2 and z1 and z2 and z1 > 0.5 and z2 > 0.5 then
l.From = p1; l.To = p2; l.Visible = true
elseif l then l.Visible = false end
end
else
for _, l in ipairs(b2.box3dLines) do l.Visible = false end
end
if not T.skeleton then
for _, l in ipairs(b2.lines) do l.Visible = false end
elseif now >= b2.nextSkeletonUpdate then
local interval = close and YF or gF
b2.nextSkeletonUpdate = HF(b2.nextSkeletonUpdate, now, interval)
for i, l in ipairs(b2.lines) do
local pr = b2.skeletonPairs[i]
if pr and l then
local p1, p2 = pr[1], pr[2]
if p1 and p2 and p1.Parent == char and p2.Parent == char then
local s1, on1, z1 = zF(cam, p1.Position)
local s2, on2, z2 = zF(cam, p2.Position)
if on1 and on2 and z1 > 0.5 and z2 > 0.5 then
l.From = s1; l.To = s2; l.Visible = true
else l.Visible = false end
else l.Visible = false end
elseif l then l.Visible = false end
end
end
end
handleReady(Players.PlayerRemoving:Connect(function(plr)
local b2 = T.bundles[plr]
if b2 then qF(b2); T.bundles[plr] = nil end
end))
local BF = false
handleReady(RunService.RenderStepped:Connect(function()
if not T.espEnabled then
if BF then PF_all() end
BF = false
return
end
BF = true
local cam = workspace.CurrentCamera
if not cam then PF_all(); return end
local char = LocalPlayer.Character
local root = char and char:FindFirstChild("HumanoidRootPart")
local myPos = root and root.Position or cam.CFrame.Position
local list = Players:GetPlayers()
local now = os.clock()
local budget = EF
for _, plr in ipairs(list) do
if plr ~= LocalPlayer then
local b2 = T.bundles[plr]
if not b2 and budget > 0 then
local ok, res = pcall(AF, plr)
if ok then b2 = res end
budget = budget - 1
end
if b2 and now >= b2.nextVisualUpdate then
pcall(XF, plr, b2, cam, myPos, now)
end
end
end
end))
local SF = {
Cabeca = { "Head" },
Tronco = { "Torso", "UpperTorso", "LowerTorso", "HumanoidRootPart" },
}
local function eF(char)
local list = SF[T.aimbotBodyPart] or SF.Cabeca
for _, n in ipairs(list) do
local p = char:FindFirstChild(n)
if p and p:IsA("BasePart") then return p end
end
return char:FindFirstChild("Head")
end
local function temLinhaDeVisao(cam, target, char)
local from = cam.CFrame.Position
local rp = RaycastParams.new()
rp.FilterType = Enum.RaycastFilterType.Exclude
rp.FilterDescendantsInstances = { LocalPlayer.Character, char, cam }
local hit = workspace:Raycast(from, target.Position - from, rp)
return hit == nil
end
local function getClosestTarget(cam, maxFov, maxDist)
local myPos = cam.CFrame.Position
local center = cam.ViewportSize / 2
local best, bestD = nil, math.huge
for _, plr in ipairs(Players:GetPlayers()) do
if plr ~= LocalPlayer and not Jz.Protected(plr) then
local char = plr.Character
local part = char and eF(char)
local hum = char and char:FindFirstChildOfClass("Humanoid")
if part and hum and hum.Health > 0 and (part.Position - myPos).Magnitude <= maxDist then
local sp, onScreen = cam:WorldToViewportPoint(part.Position)
if onScreen and sp.Z > 0 then
local d = (Vector2.new(sp.X, sp.Y) - center).Magnitude
if d <= maxFov and d < bestD then
if not T.aimbotWallCheck or temLinhaDeVisao(cam, part, char) then
best, bestD = part, d
end
end
end
end
end
end
return best
end
local aimbotConnection = nil
local function aimbotLoop()
if aimbotConnection then return end
aimbotConnection = RunService.RenderStepped:Connect(function()
if not T.aimbotEnabled then return end
local cam = workspace.CurrentCamera
if not cam then return end
local char = LocalPlayer.Character
local hum = char and char:FindFirstChildOfClass("Humanoid")
if not hum or hum.Health <= 0 then return end
if T.aimbotMode == "Legit" then
local tool = char:FindFirstChildOfClass("Tool")
if not tool then return end
end
local target = getClosestTarget(cam, T.aimbotFov, T.aimbotDistance)
if not target then return end
local myPos = cam.CFrame.Position
local targetPos = target.Position
if T.aimbotMode == "Rage" then
cam.CFrame = CFrame.new(myPos, targetPos)
else
local currentLook = cam.CFrame.LookVector
local desiredLook = (targetPos - myPos).Unit
local smooth = math.clamp(T.aimbotSmoothness, 0.01, 1)
local factor = math.clamp(smooth * 8, 0.02, 0.35)
local dot = currentLook:Dot(desiredLook)
if dot > 0.998 then factor = 0.6 end
local newLook = currentLook:Lerp(desiredLook, factor)
cam.CFrame = CFrame.new(myPos, myPos + newLook)
end
end)
end
local function stopAimbot()
if aimbotConnection then aimbotConnection:Disconnect(); aimbotConnection = nil end
end
o.aimbotLoop = aimbotLoop
o.stopAimbot = stopAimbot
do
local lastClick, warned = -math.huge, false
local function visible(gui)
local node = gui
while node do
if node:IsA("GuiObject") and not node.Visible then return false end
if node:IsA("ScreenGui") and not node.Enabled then return false end
node = node.Parent
end
return true
end
local function findGame()
local pg = LocalPlayer:FindFirstChild("PlayerGui")
local root = pg and pg:FindFirstChild("Lockpick", true)
if not root or not visible(root) then return nil end
local inner = root:FindFirstChild("Lockpick")
if not inner or not inner:IsA("GuiObject") then inner = root end
local block = inner:FindFirstChild("Bloco", true)
local goal = inner:FindFirstChild("Objetivo", true)
local button = inner:FindFirstChild("Btn", true)
if not block or not goal or not button or not block:IsA("GuiObject")
or not goal:IsA("GuiObject") or not button:IsA("GuiButton") then return nil end
if not visible(block) or not visible(goal) or not visible(button) or not button.Active then return nil end
if block.AbsoluteSize.X <= 0 or goal.AbsoluteSize.Y <= 0 then return nil end
return block, goal, button
end
local function aligned(block, goal)
local bp, bs = block.AbsolutePosition, block.AbsoluteSize
local gp, gs = goal.AbsolutePosition, goal.AbsoluteSize
local cx, cy = bp.X + bs.X * 0.5, bp.Y + bs.Y * 0.5
local padX = math.min(gs.X * 0.12, 4)
local padY = math.min(gs.Y * 0.12, 4)
return cx >= gp.X + padX and cx <= gp.X + gs.X - padX
and cy >= gp.Y + padY and cy <= gp.Y + gs.Y - padY
end
local function activate(button)
if type(getconnections) == "function" then
for _, name in ipairs({ "Activated", "MouseButton1Click" }) do
local ok, connections = pcall(getconnections, button[name])
if ok and type(connections) == "table" then
local count = 0
for _, conn in ipairs(connections) do
if conn.Enabled ~= false and conn.ForeignState ~= true then
if type(conn.Fire) == "function" then
if pcall(function() conn:Fire() end) then count = count + 1 end
elseif type(conn.Function) == "function" then
local callback = conn.Function
task.spawn(function() pcall(callback) end)
count = count + 1
end
end
end
if count > 0 then return true end
end
end
end
if type(firesignal) == "function" then
return pcall(firesignal, button.Activated)
end
return false
end
function o.setPerfectLockpick(v)
T.perfectLockpick = v == true
lastClick, warned = -math.huge, false
end
handleReady(RunService.RenderStepped:Connect(function()
if not T.perfectLockpick or o.destroyed or os.clock() - lastClick < 0.16 then return end
local ok, block, goal, button = pcall(findGame)
if not ok or not block or not aligned(block, goal) then return end
lastClick = os.clock()
local fired, result = pcall(activate, button)
if (not fired or not result) and not warned then
warned = true
Xz("Auto Lockpick: este executor nao disponibiliza acesso aos botoes.")
end
end))
end
do
local I = {}
o.interact = I
function I.position(prompt)
local parent = prompt and prompt.Parent
if not parent then return nil end
if parent:IsA("Attachment") then return parent.WorldPosition end
if parent:IsA("BasePart") then return parent.Position end
if parent:IsA("Model") then return parent:GetPivot().Position end
local part = parent:FindFirstAncestorWhichIsA("BasePart")
return part and part.Position
end
function I.prompt(prompt, active, nativeOnly)
local root = isRoot()
local position = I.position(prompt)
if not root or not position or not prompt.Enabled then return false end
if (root.Position - position).Magnitude > prompt.MaxActivationDistance then return false end
if active and not active() then return false end
if not nativeOnly and type(fireproximityprompt) == "function" then
local sent = pcall(fireproximityprompt, prompt, math.max(0.1, prompt.HoldDuration))
if sent then return true end
end
local began = pcall(function() prompt:InputHoldBegin() end)
if not began then return false end
local deadline = os.clock() + math.max(prompt.HoldDuration, 0) + 0.1
repeat
if not prompt.Parent or not prompt.Enabled or (active and not active()) then
pcall(function() prompt:InputHoldEnd() end)
return false
end
task.wait(0.04)
until os.clock() >= deadline
return pcall(function() prompt:InputHoldEnd() end)
end
function o.teleportCharacter(cf)
local char, root = LocalPlayer.Character, isRoot()
if not char or not root or root.Anchored then return false end
local offset = root.CFrame:ToObjectSpace(char:GetPivot())
char:PivotTo(cf * offset)
root.AssemblyLinearVelocity, root.AssemblyAngularVelocity = Vector3.zero, Vector3.zero
return true
end
local M = { owner = nil, token = 0 }
o.travel = M
function M.cancel(owner)
if owner == nil or owner == M.owner then M.token = M.token + 1 end
end
function M.to(owner, destination, active)
if M.owner then return false, "Outro deslocamento esta em andamento." end
local char, root, hum = LocalPlayer.Character, isRoot(), isCValid()
if not char or not root or not hum or hum.Health <= 0 then return false, "Personagem indisponivel." end
if hum.SeatPart then return false, "Saia do assento antes do deslocamento." end
if T.flyEnabled or T.ghostEnabled then return false, "Desative Fly e Ghost antes do deslocamento." end
M.owner, M.token = owner, M.token + 1
local token = M.token
o.travelBusy = true
o.noclip.acquire("travel")
local function running()
return not o.destroyed and M.token == token and char == LocalPlayer.Character
and root.Parent and hum.Health > 0 and (not active or active())
end
local function getDestination()
if type(destination) == "function" then return destination() end
return destination
end
local ok, success, message = pcall(function()
local first = getDestination()
if not first then return false, "Destino indisponivel." end
if not running() or not o.teleportCharacter(CFrame.new(first)) then return false, "TP indisponivel." end
task.wait(0.25)
if not running() then return false, "TP cancelado." end
if (root.Position - first).Magnitude > 6 then return false, "O jogo recusou o TP direto." end
return true
end)
if root.Parent then root.AssemblyLinearVelocity, root.AssemblyAngularVelocity = Vector3.zero, Vector3.zero end
o.noclip.release("travel")
M.owner, o.travelBusy = nil, false
if not ok then return false, "Falha no deslocamento: " .. tostring(success) end
return success, message
end
end
do
local function rootsFor(obj)
local roots, seen = {}, {}
local parts = obj:IsA("BasePart") and { obj } or obj:GetDescendants()
for _, part in ipairs(parts) do
if part:IsA("BasePart") then
if part.Anchored then return nil, "O objeto contem fixado pelo servidor." end
local root = part.AssemblyRootPart or part
if root ~= obj and not root:IsDescendantOf(obj) then return nil, "O objeto esta ligado a outra estrutura." end
if root.Anchored then return nil, "A estrutura contem fixada pelo servidor." end
if not seen[root] then seen[root] = true; roots[#roots + 1] = root end
end
end
return roots
end
local function canMove(obj)
if not obj or not obj.Parent or not obj:IsDescendantOf(workspace) then return false, "Objeto indisponivel." end
local roots, reason = rootsFor(obj)
if not roots then return false, reason end
if #roots == 0 then return false, "Objeto sem partes fisicas." end
if type(isnetworkowner) ~= "function" then return false, "O executor nao informa o controle fisico deste objeto." end
for _, root in ipairs(roots) do
local ok, owned = pcall(isnetworkowner, root)
if not ok or owned ~= true then return false, "O servidor nao concede controle fisico deste objeto." end
end
return true, roots
end
o.worldMove = { canMove = canMove }
function o.worldMove.move(obj, destination, active)
local allowed, roots = canMove(obj)
if not allowed then return false, roots end
if o.destroyed or (active and not active()) then return false, "Acao cancelada." end
for _, root in ipairs(roots) do root.AssemblyLinearVelocity, root.AssemblyAngularVelocity = Vector3.zero, Vector3.zero end
if obj:IsA("Model") then
obj:PivotTo(destination)
elseif obj:IsA("BasePart") then
obj.CFrame = destination
else
local handle = obj:IsA("Tool") and obj:FindFirstChild("Handle")
if not handle then return false, "Objeto sem modelo movivel." end
handle.CFrame = destination
end
task.wait(0.4)
if not obj.Parent or o.destroyed or (active and not active()) then return false, "Acao interrompida." end
local pivot = obj:IsA("Model") and obj:GetPivot()
or (obj:IsA("BasePart") and obj.CFrame) or obj:FindFirstChild("Handle").CFrame
if (pivot.Position - destination.Position).Magnitude > 5 then return false, "O jogo recusou o movimento do objeto." end
return true
end
function o.worldMove.toPosition(obj, reference, position, active)
local delta = position - reference.Position
local pivot = obj:IsA("Model") and obj:GetPivot() or reference.CFrame
return o.worldMove.move(obj, pivot + delta, active)
end
end
local ZF, DF, rF, hF, jF, WF = 20, 14, 1.5, D, 18, 65
local WHITELIST_ARMAS = {
ninja = true, ak47 = true, fal = true, fuzil = true, glock = true,
oit_o = true, oit_o_ = true, submetralhadora = true, usps = true, uzi = true,
barret = true, fuzilhollow = true, fuzilphantom = true,
fuziltrovao = true, fuziltrovao_ = true, g3 = true,
ptdragon = true, ptneon = true, ptnk = true,
armapdsecreta = true, fuzilninja = true, fuzilpascoa = true,
natalina = true, ptninja = true, ptpascoa = true,
}
local function normalizarNome(nome)
if not nome then return "" end
local n = tostring(nome)
n = n:gsub("[áàãâä]", "a"):gsub("[éèêë]", "e"):gsub("[íìîï]", "i")
n = n:gsub("[óòõôö]", "o"):gsub("[úùûü]", "u"):gsub("ç", "c")
return n:lower():gsub("[^%w]", "")
end
local function ehArmaPermitida(nome)
local key = normalizarNome(nome)
if key == "" then return false end
if WHITELIST_ARMAS[key] then return true end
for arma in pairs(WHITELIST_ARMAS) do
if (#arma >= 4 and key:find(arma, 1, true)) or (#arma <= 3 and key:match("^" .. arma .. "%d*$")) then return true end
end
return false
end
o.ehArmaPermitida = ehArmaPermitida
local POSICAO_RETORNO = Vector3.new(-821.86, 129.42, -4681.15)
local GF
do
local inventoryPoll = 0.35
local inventoryBaseline, warmUntil = nil, os.clock() + 0.75
local enabled = true
local function status(message)
o.coletaStatus = tostring(message)
if o.coletaStatusLabel then pcall(function() o.coletaStatusLabel:Set(o.coletaStatus, true) end) end
end
local function textWeapon(text)
local name = normalizarNome(text)
return ehArmaPermitida(name) or name == "arma" or name == "weapon" or name == "gun"
end
local function itemName(obj)
for _, field in ipairs({ "WeaponName", "ItemName", "NomeArma", "NomeItem", "Nome", "Name", "Arma", "Item" }) do
local value = obj:GetAttribute(field)
if type(value) == "string" and textWeapon(value) then return value end
local child = obj:FindFirstChild(field)
if child and child:IsA("StringValue") and textWeapon(child.Value) then return child.Value end
end
if obj:IsA("StringValue") and textWeapon(obj.Value) then return obj.Value end
if obj:IsA("ObjectValue") and obj.Value then
local reference = obj.Value
if textWeapon(reference.Name) then return reference.Name end
end
if textWeapon(obj.Name) then return obj.Name end
if obj:GetAttribute("IsWeapon") == true or obj:GetAttribute("Weapon") == true then return obj.Name end
for _, field in ipairs({ "ItemType", "Category", "Type" }) do
local value = normalizarNome(obj:GetAttribute(field))
if value == "arma" or value == "weapon" or value == "gun" then return obj.Name end
end
if obj:IsA("Tool") then
for _, field in ipairs({ "Ammo", "Mag", "Magazine", "GunSettings", "ACS_Settings", "WeaponConfig" }) do
if obj:FindFirstChild(field, true) then return obj.Name end
end
end
end
local function weapon(obj)
if itemName(obj) then return true end
for _, child in ipairs(obj:GetDescendants()) do
if itemName(child) then return true end
if child:IsA("ProximityPrompt") then
local text = (child.ActionText .. " " .. child.ObjectText):lower()
if textWeapon(child.ObjectText) or text:find("%f[%w]arma%f[%W]") or text:find("%f[%w]weapon%f[%W]") then return true end
end
end
return false
end
local function quantity(obj)
if obj:IsA("NumberValue") or obj:IsA("IntValue") then return math.max(0, obj.Value) end
if obj:IsA("BoolValue") then return obj.Value and 1 or 0 end
for _, name in ipairs({ "Quantity", "Quantidade", "Amount", "Count", "Stack" }) do
local value = tonumber(obj:GetAttribute(name))
local child = obj:FindFirstChild(name)
if not value and child and (child:IsA("NumberValue") or child:IsA("IntValue")) then value = child.Value end
if value then return math.max(0, value) end
end
return 1
end
local function inventoryRoots()
local roots, seen = {}, {}
local function add(root)
if root and not seen[root] then seen[root] = true; roots[#roots + 1] = root end
end
for _, parent in pairs({ LocalPlayer, LocalPlayer:FindFirstChild("Status"), LocalPlayer:FindFirstChild("Data"), LocalPlayer:FindFirstChild("PlayerData") }) do
for _, name in ipairs({ "Inv", "Inventory", "Inventario", "Inventário", "InventoryData", "Mochila", "Items", "Itens", "Armas", "Weapons" }) do
add(parent:FindFirstChild(name))
end
end
for _, name in ipairs({ "Inventories", "Inventarios", "InventoryData" }) do
local parent = ReplicatedStorage:FindFirstChild(name)
if parent then add(parent:FindFirstChild(tostring(LocalPlayer.UserId)) or parent:FindFirstChild(LocalPlayer.Name)) end
end
return roots
end
local function snapshot()
local counts = {}
local function keyFor(name)
local key = normalizarNome(name)
return key == "ninja" and "fuzilninja" or key
end
for _, parent in pairs({ LocalPlayer.Character, LocalPlayer:FindFirstChild("Backpack") }) do
for _, tool in ipairs(parent:GetChildren()) do
if tool:IsA("Tool") then
local name = itemName(tool)
if name then local key = keyFor(name); counts[key] = (counts[key] or 0) + 1 end
end
end
end
for _, root in ipairs(inventoryRoots()) do
local backend = {}
local function visit(node)
local name = itemName(node)
if name then
local key = keyFor(name)
backend[key] = (backend[key] or 0) + quantity(node)
else
for _, child in ipairs(node:GetChildren()) do visit(child) end
end
end
for _, child in ipairs(root:GetChildren()) do visit(child) end
for key, count in pairs(backend) do counts[key] = math.max(counts[key] or 0, count) end
end
return counts
end
local function gained(before, current)
current = current or snapshot()
for key, count in pairs(current) do if count > (before[key] or 0) then return true, key end end
return false
end
local function partFor(obj)
if obj:IsA("BasePart") then return obj end
return (obj:IsA("Model") and obj.PrimaryPart) or obj:FindFirstChildWhichIsA("BasePart", true)
end
local records = setmetatable({}, { __mode = "k" })
local queued = setmetatable({}, { __mode = "k" })
local queue, head, tail = {}, 1, 0
local folders, characters = {}, {}
local indexStarted, demandUntil, rootsAt = false, 0, -math.huge
local folderNames = { "DrogadosInv", "Drops", "ItensDropados", "ItensNoChao", "ItemsDrop", "DropsInv", "ArmasDropadas" }
local function enqueue(obj)
if not obj or queued[obj] then return end
tail = tail + 1; queue[tail] = obj; queued[obj] = true
end
local function excluded(obj)
local own = LocalPlayer.Character
if own and (obj == own or obj:IsDescendantOf(own)) then return true end
for _, char in ipairs(characters) do
if obj == char or obj:IsDescendantOf(char) then return true end
end
return false
end
local function inScope(obj)
if #folders == 0 then return obj:IsDescendantOf(workspace) end
for _, folder in ipairs(folders) do
if obj == folder or obj:IsDescendantOf(folder) then return true end
end
return false
end
local function addRecord(obj, prompt, evidence)
if not obj or excluded(obj) or not inScope(obj) then return end
if not (obj:IsA("Model") or obj:IsA("Tool") or obj:IsA("BasePart")) then return end
local name = itemName(obj)
if not name and not evidence then return end
local part = partFor(obj)
if not part then return end
local parent = obj.Parent
for _ = 1, 8 do
if not parent or parent == workspace then break end
if records[parent] then
if prompt then records[parent].prompt = prompt end
return
end
parent = parent.Parent
end
local existing = records[obj]
records[obj] = {
item = obj, parte = part, prompt = prompt or (existing and existing.prompt),
nome = name or (prompt and prompt.ObjectText ~= "" and prompt.ObjectText) or obj.Name,
}
end
local function inspect(obj)
if excluded(obj) or not inScope(obj) then return end
if obj:IsA("ProximityPrompt") then
if not obj.Enabled or obj.MaxActivationDistance <= 0 then return end
local parent = obj.Parent
if parent and parent:IsA("Attachment") then parent = parent.Parent end
if not parent then return end
local candidate, cursor = parent, parent
for _ = 1, 10 do
if not cursor or cursor == workspace or cursor:IsA("Folder") then break end
if (cursor:IsA("Model") or cursor:IsA("Tool")) and itemName(cursor) then candidate = cursor; break end
cursor = cursor.Parent
end
local text = (obj.ActionText .. " " .. obj.ObjectText):lower()
local evidence = textWeapon(obj.ObjectText) or text:find("%f[%w]arma%f[%W]") or text:find("%f[%w]weapon%f[%W]")
if candidate == parent and evidence then
local model = parent:FindFirstAncestorWhichIsA("Model")
if model and model.Parent and not excluded(model) and inScope(model) then
local isDropModel = false
for _, folder in ipairs(folders) do
if model.Parent == folder then isDropModel = true; break end
end
if not isDropModel and #folders == 0 then
local children = model:GetChildren()
if #children <= 24 and not model:FindFirstChildOfClass("Humanoid") then
isDropModel = true
for _, child in ipairs(children) do
if child:IsA("Folder") or child:IsA("Model") then isDropModel = false; break end
end
end
end
if isDropModel then candidate = model end
end
end
addRecord(candidate, obj, evidence)
elseif obj:IsA("Model") or obj:IsA("Tool") or obj:IsA("BasePart") then
addRecord(obj)
end
end
local function updateRoots()
characters = {}
for _, player in ipairs(Players:GetPlayers()) do
if player.Character then characters[#characters + 1] = player.Character end
end
local newFolders = {}
for _, name in ipairs(folderNames) do
local folder = workspace:FindFirstChild(name)
if folder then
newFolders[#newFolders + 1] = folder
if not table.find(folders, folder) then enqueue(folder) end
end
end
local fallback = #newFolders == 0 and (not indexStarted or #folders > 0)
folders, indexStarted, rootsAt = newFolders, true, os.clock()
if fallback then enqueue(workspace) end
end
handleReady(workspace.DescendantAdded:Connect(function(obj)
if not indexStarted or not inScope(obj) or excluded(obj) then return end
enqueue(obj)
local parent = obj.Parent
for _ = 1, 4 do
if not parent or parent == workspace or parent:IsA("Folder") then break end
enqueue(parent); parent = parent.Parent
end
end))
handleReady(workspace.ChildAdded:Connect(function(obj)
if table.find(folderNames, obj.Name) then rootsAt = -math.huge end
end))
task.spawn(function()
while enabled and not o.destroyed do
if T.autoColetaEnabled or T.coletaHudEnabled or os.clock() < demandUntil then
local ok, reason = pcall(function()
if os.clock() - rootsAt >= 3 then updateRoots() end
local processed, deadline = 0, os.clock() + 0.004
while head <= tail and processed < 120 and os.clock() < deadline do
local obj = queue[head]; queue[head] = nil; head = head + 1
queued[obj] = nil; processed = processed + 1
if obj and (obj == workspace or obj.Parent) and not excluded(obj) then
if obj ~= workspace then inspect(obj) end
if obj == workspace or obj:IsA("Folder") or obj:IsA("Model") or obj:IsA("Tool") or obj:IsA("BasePart") or obj:IsA("Attachment") then
local children = obj:GetChildren()
for i, child in ipairs(children) do
enqueue(child)
if i % 120 == 0 then task.wait(); if not enabled or o.destroyed then return end end
end
end
end
end
if head > tail then queue, head, tail = {}, 1, 0 end
o.dropIndexBusy = head <= tail
end)
if not ok then status("Falha ao indexar drops: " .. tostring(reason)) end
task.wait(o.dropIndexBusy and 0.03 or 0.15)
else task.wait(0.2) end
end
end)
local function refreshDrops()
demandUntil = os.clock() + 3
if not indexStarted then o.dropIndexBusy = true end
characters = {}
for _, player in ipairs(Players:GetPlayers()) do
if player.Character then characters[#characters + 1] = player.Character end
end
local root, out = isRoot(), {}
if not root then return out end
for obj, record in pairs(records) do
if not obj.Parent or not record.parte.Parent or not obj:IsDescendantOf(workspace) or excluded(obj) or not inScope(obj) then
records[obj] = nil
else
local nested, parent = false, obj.Parent
for _ = 1, 8 do
if not parent or parent == workspace then break end
if records[parent] then nested = true; break end
parent = parent.Parent
end
if not nested then
record.distancia = (root.Position - record.parte.Position).Magnitude
out[#out + 1] = record
end
end
end
table.sort(out, function(a, b) return a.distancia < b.distancia end)
return out
end
o.listDrops, o.isWeaponObject, o.inventoryWeaponSnapshot = refreshDrops, weapon, snapshot
local function returnToBase(active)
status("Arma recebida; retornando ao ponto")
local returned, reason = o.travel.to("collection", POSICAO_RETORNO, active)
status(returned and "Retorno concluido" or (reason or "Retorno interrompido"))
return returned
end
local function useTarget(target, active, nativeOnly)
local prompt = target.prompt
if not prompt or not prompt.Parent then prompt = target.item:FindFirstChildWhichIsA("ProximityPrompt", true) end
if prompt and prompt.Enabled then return o.interact.prompt(prompt, active, nativeOnly) end
local click = target.item:FindFirstChildWhichIsA("ClickDetector", true)
if click and type(fireclickdetector) == "function" then return pcall(fireclickdetector, click) end
local handle = target.item:FindFirstChild("Handle", true) or target.parte
local root = isRoot()
if root and handle and type(firetouchinterest) == "function" then
pcall(firetouchinterest, root, handle, 0); task.wait(0.1)
return pcall(firetouchinterest, root, handle, 1)
end
return false
end
GF = function(target, checkDone, pullOverride)
if o.collectionBusy or o.travel.owner then return false, "Aguarde a acao atual." end
if not target or not target.item or not target.parte or not target.item.Parent or not target.parte.Parent then return false, "Item indisponivel." end
local snapshotOK, before = pcall(snapshot)
if not snapshotOK then return false, "Inventario indisponivel: " .. tostring(before) end
o.collectionBusy = true
o.itemTeleportRunId = o.itemTeleportRunId + 1
local run, char = o.itemTeleportRunId, LocalPlayer.Character
local interacted = false
local function active() return enabled and not o.destroyed and run == o.itemTeleportRunId and LocalPlayer.Character == char end
local function received()
if checkDone then return checkDone() end
return gained(before) or (interacted and not target.item:IsDescendantOf(workspace))
end
local ok, success, message = pcall(function()
status("Coletando " .. tostring(target.nome or target.item.Name))
if not checkDone and (pullOverride == true or (pullOverride == nil and T.pullWeaponsEnabled)) then
local root = isRoot()
if not root then return false, "Personagem indisponivel." end
local point = root.Position + root.CFrame.LookVector * 3 + Vector3.new(0, -1, 0)
local moved, why = o.worldMove.toPosition(target.item, target.parte, point, active)
if not moved then return false, why end
else
local function destination()
if not target.item.Parent or not target.parte.Parent then return nil end
local prompt = target.prompt or target.item:FindFirstChildWhichIsA("ProximityPrompt", true)
local position = prompt and o.interact.position(prompt) or target.parte.Position
local above = target.parte.Size.Y * 0.5 + 3
if prompt then above = math.min(above, prompt.MaxActivationDistance * 0.65) end
return position and position + Vector3.new(0, above, 0)
end
local arrived, why = o.travel.to("collection", destination, function() return active() and not received() end)
if not arrived and not received() then return false, why end
end
for attempt = 1, 5 do
if not active() or received() or not target.item.Parent then break end
interacted = useTarget(target, active, attempt % 2 == 0) or interacted
local deadline = os.clock() + 0.6
repeat task.wait(0.1) until not active() or received() or os.clock() >= deadline
end
if not active() then return false, "Coleta cancelada." end
local acknowledgeUntil = os.clock() + 3
while active() and not received() and os.clock() < acknowledgeUntil do task.wait(0.1) end
if not active() then return false, "Coleta cancelada." end
if not received() then return false, "O jogo nao confirmou a arma nos dados do inventario." end
inventoryBaseline = snapshot()
if not checkDone and T.returnOnWeaponPickup ~= false then
local returned = returnToBase(active)
if not returned then return false, o.coletaStatus end
end
return true, "Arma recebida."
end)
o.collectionBusy = false
if not ok then status("Falha na coleta: " .. tostring(success)); return false, o.coletaStatus end
if not success then status(message or "Coleta interrompida") end
return success, message
end
o.coletarItem = GF
function o.stopAutoColeta()
T.autoColetaEnabled = false
o.coletaRunId, o.itemTeleportRunId = o.coletaRunId + 1, o.itemTeleportRunId + 1
o.travel.cancel("collection")
if o.coletaControl then o.coletaControl:Set(false, true) end
end
function o.startAutoColeta()
if T.autoFarmPumpkinEnabled and o.startPumpkin then o.startPumpkin(false) end
if T.autoFarmLixeiroEnabled and o.startLixeiro then o.startLixeiro(false) end
if o.collectionWorker and o.collectionWorker.running and T.autoColetaEnabled then return end
if o.startPesca and T.autoFarmPescaEnabled then o.startPesca(false) end
T.autoColetaEnabled, o.coletaRunId = true, o.coletaRunId + 1
local run, worker = o.coletaRunId, { running = true }
o.collectionWorker = worker
if o.coletaControl then o.coletaControl:Set(true, true) end
task.spawn(function()
local ok, reason = pcall(function()
local retryAfter = setmetatable({}, { __mode = "k" })
while enabled and T.autoColetaEnabled and not o.destroyed and o.coletaRunId == run do
if not o.collectionBusy and not o.travel.owner then
local target
for _, candidate in ipairs(refreshDrops()) do
if (retryAfter[candidate.item] or 0) <= os.clock() then target = candidate; break end
end
if target then
local collected, why = GF(target)
if not collected then
retryAfter[target.item] = os.clock() + 4
status(why or "Coleta recusada")
end
else status("Aguardando arma dropada carregada no mapa") end
end
task.wait(math.max(0.25, tonumber(T.autoColetaCooldown) or 0.35))
end
end)
worker.running = false
if not ok and o.coletaRunId == run then
T.autoColetaEnabled = false
if o.coletaControl then o.coletaControl:Set(false, true) end
status("Erro na Auto Coleta: " .. tostring(reason))
Xz(o.coletaStatus)
end
end)
end
local function manualReturn()
if o.collectionBusy or o.destroyed or T.returnOnWeaponPickup == false then return end
o.collectionBusy = true
if T.autoFarmPumpkinEnabled and o.startPumpkin then o.startPumpkin(false) end
if T.autoFarmLixeiroEnabled and o.startLixeiro then o.startLixeiro(false) end
if T.autoFarmPescaEnabled and o.startPesca then o.startPesca(false) end
if T.farmPartesEnabled and o.pararFarmPartes then o.pararFarmPartes() end
o.travel.cancel()
o.itemTeleportRunId = o.itemTeleportRunId + 1
local run, char = o.itemTeleportRunId, LocalPlayer.Character
task.spawn(function()
local function active() return enabled and not o.destroyed and T.returnOnWeaponPickup ~= false and o.itemTeleportRunId == run and LocalPlayer.Character == char end
local deadline = os.clock() + 3
while o.travel.owner and active() and os.clock() < deadline do task.wait(0.05) end
local ok, reason = pcall(function() if active() then returnToBase(active) end end)
o.collectionBusy = false
if not ok then status("Erro no retorno: " .. tostring(reason)) end
end)
end
task.spawn(function()
while enabled and not o.destroyed do
task.wait(inventoryPoll)
local ok, current = pcall(snapshot)
if ok then
local previous = inventoryBaseline
inventoryBaseline = current
if previous and os.clock() >= warmUntil and gained(previous, current) and not o.collectionBusy then manualReturn() end
end
end
end)
handleReady(LocalPlayer.CharacterAdded:Connect(function()
inventoryBaseline, warmUntil = nil, os.clock() + 0.75
end))
inventoryBaseline = snapshot()
function o.cleanupCollection() enabled = false; o.stopAutoColeta() end
end
do
local hudGui = Instance.new("ScreenGui")
hudGui.Name = "VeteranoColetaHUD"
hudGui.ResetOnSpawn = false
hudGui.IgnoreGuiInset = true
hudGui.DisplayOrder = 9997
if protect_gui then pcall(protect_gui, hudGui) end
hudGui.Parent = LocalPlayer:WaitForChild("PlayerGui")
o.coletaHudGui = hudGui
local frame = Instance.new("Frame")
frame.Name = "ColetaHUD"
frame.AnchorPoint = Vector2.new(1, 0)
frame.Position = UDim2.new(1, -20, 0, 100)
frame.Size = UDim2.fromOffset(280, 320)
frame.BackgroundColor3 = Color3.fromRGB(22, 14, 34)
frame.BorderSizePixel = 0
frame.Active = true
frame.Visible = false
frame.Parent = hudGui
Instance.new("UICorner", frame).CornerRadius = UDim.new(0, 12)
local fs = Instance.new("UIStroke"); fs.Color = Color3.fromRGB(100, 60, 160); fs.Thickness = 1; fs.Parent = frame
local header = Instance.new("Frame", frame)
header.Size = UDim2.new(1, 0, 0, 30)
header.BackgroundTransparency = 1
local title = Instance.new("TextLabel", header)
title.BackgroundTransparency = 1
title.Position = UDim2.fromOffset(14, 0)
title.Size = UDim2.new(1, -90, 1, 0)
title.Font = Enum.Font.GothamBold
title.Text = "Armas no chao"
title.TextColor3 = Color3.fromRGB(240, 230, 255)
title.TextSize = 13
title.TextXAlignment = Enum.TextXAlignment.Left
local countLbl = Instance.new("TextLabel", header)
countLbl.AnchorPoint = Vector2.new(1, 0.5)
countLbl.Position = UDim2.new(1, -14, 0.5, 0)
countLbl.Size = UDim2.fromOffset(80, 20)
countLbl.BackgroundTransparency = 1
countLbl.Font = Enum.Font.Gotham
countLbl.Text = "0"
countLbl.TextColor3 = Color3.fromRGB(170, 140, 210)
countLbl.TextSize = 11
countLbl.TextXAlignment = Enum.TextXAlignment.Right
local list = Instance.new("ScrollingFrame", frame)
list.BackgroundTransparency = 1
list.BorderSizePixel = 0
list.Position = UDim2.fromOffset(10, 38)
list.Size = UDim2.new(1, -20, 1, -48)
list.ScrollBarThickness = 3
list.ScrollBarImageColor3 = Color3.fromRGB(168, 100, 255)
list.CanvasSize = UDim2.new()
list.AutomaticCanvasSize = Enum.AutomaticSize.Y
local ll = Instance.new("UIListLayout")
ll.Padding = UDim.new(0, 5)
ll.SortOrder = Enum.SortOrder.LayoutOrder
ll.Parent = list
local emptyMsg = Instance.new("TextLabel", list)
emptyMsg.Name = "EmptyMsg"
emptyMsg.Size = UDim2.new(1, 0, 0, 30)
emptyMsg.BackgroundTransparency = 1
emptyMsg.Font = Enum.Font.Gotham
emptyMsg.Text = "Nenhuma arma dropada"
emptyMsg.TextColor3 = Color3.fromRGB(170, 140, 210)
emptyMsg.TextSize = 12
emptyMsg.Visible = true
local rows = {}
local function makeRow(i)
local row = Instance.new("Frame", list)
row.Size = UDim2.new(1, -4, 0, 34)
row.BackgroundColor3 = Color3.fromRGB(32, 20, 50)
row.BorderSizePixel = 0
row.LayoutOrder = i
Instance.new("UICorner", row).CornerRadius = UDim.new(0, 6)
local label = Instance.new("TextLabel", row)
label.Position = UDim2.fromOffset(9, 0)
label.Size = UDim2.new(1, -62, 1, 0)
label.BackgroundTransparency = 1
label.Font = Enum.Font.Gotham
label.Text = ""
label.TextColor3 = Color3.fromRGB(240, 230, 255)
label.TextSize = 11
label.TextTruncate = Enum.TextTruncate.AtEnd
label.TextXAlignment = Enum.TextXAlignment.Left
local tp = Instance.new("TextButton", row)
tp.AnchorPoint = Vector2.new(1, 0.5)
tp.Position = UDim2.new(1, -5, 0.5, 0)
tp.Size = UDim2.fromOffset(42, 24)
tp.BackgroundColor3 = Color3.fromRGB(168, 100, 255)
tp.Font = Enum.Font.GothamBold
tp.Text = "TP"
tp.TextColor3 = Color3.fromRGB(18, 10, 30)
tp.TextSize = 11
tp.AutoButtonColor = false
Instance.new("UICorner", tp).CornerRadius = UDim.new(0, 5)
local entry = { frame = row, label = label, button = tp }
rows[i] = entry
handleReady(tp.Activated:Connect(function()
local current = entry.target
if entry.busy or not current or not current.item.Parent then return end
entry.busy, tp.Text = true, "..."
task.spawn(function()
local ok, success, reason = pcall(o.coletarItem, current)
entry.busy, tp.Text = false, "TP"
if not ok or not success then Xz(ok and (reason or "Coleta recusada") or tostring(success)) end
end)
end))
return entry
end
local function refresh()
local drops = o.listDrops and o.listDrops() or {}
local shown = math.min(#drops, 20)
countLbl.Text = tostring(#drops)
emptyMsg.Visible = shown == 0
emptyMsg.Text = o.dropIndexBusy and "Buscando armas carregadas..." or "Nenhuma arma dropada"
for i = 1, shown do
local entry = rows[i] or makeRow(i)
local d = drops[i]
entry.target = d
entry.frame.Visible = true
entry.label.Text = string.format("%s - %dm", tostring(d.nome), math.floor(d.distancia + 0.5))
end
for i = shown + 1, #rows do rows[i].target = nil; rows[i].frame.Visible = false end
end
function o.setColetaHudVisible(v)
T.coletaHudEnabled = v == true
frame.Visible = T.coletaHudEnabled
if frame.Visible then
emptyMsg.Text = "Buscando armas carregadas..."
task.defer(function() if frame.Visible and not o.destroyed then pcall(refresh) end end)
end
end
task.spawn(function()
while not o.destroyed do
if frame.Visible then pcall(refresh) end
task.wait(0.6)
end
end)
end
do
local function tF()
local folder = workspace:FindFirstChild("FarmPartesDeArma")
folder = folder and folder:FindFirstChild("Locais")
if not folder then return nil end
for _, obj in ipairs(folder:GetChildren()) do
if obj:IsA("BasePart") and obj:FindFirstChild("GUI") then return obj end
end
local root = isRoot()
if not root then return nil end
local best, bestD
for _, obj in ipairs(folder:GetChildren()) do
local prompt = obj:IsA("BasePart") and obj:FindFirstChild("Interagir")
if prompt and prompt.Enabled then
local d = (root.Position - obj.Position).Magnitude
if not bestD or d < bestD then best, bestD = obj, d end
end
end
return best
end
local function KF()
o.farmPartesRunId = o.farmPartesRunId + 1
o.itemTeleportRunId = o.itemTeleportRunId + 1
if o.travel then o.travel.cancel("collection") end
T.farmPartesEnabled = false
end
local Cf = 15
local function Yf()
if T.autoFarmPumpkinEnabled and o.startPumpkin then o.startPumpkin(false) end
if T.flyEnabled then T.flyEnabled = false; pcall(xz) end
o.farmPartesRunId = o.farmPartesRunId + 1
local runId = o.farmPartesRunId
local count = 0
local hadNoclip = T.noclipEnabled
if not hadNoclip then T.noclipEnabled = true; pcall(Nz) end
while T.farmPartesEnabled and not o.destroyed and runId == o.farmPartesRunId do
local alvo = tF()
if not alvo then
task.wait(1)
else
local target = alvo
local reached = GF({ item = target, parte = target }, function() return tF() ~= target end)
if runId ~= o.farmPartesRunId then break end
local moved = reached and tF() ~= target
if moved then count = count + 1 end
local wait = moved and Cf or 1
local deadline = os.clock() + wait
while os.clock() < deadline do
if not T.farmPartesEnabled or o.destroyed or runId ~= o.farmPartesRunId then break end
task.wait(0.2)
end
end
end
if runId == o.farmPartesRunId then
T.farmPartesEnabled = false
o.farmPartesColetadas = count
end
if not hadNoclip and (not T.farmPartesEnabled or runId == o.farmPartesRunId) then
T.noclipEnabled = false
pcall(Nz)
end
return count
end
o.iniciarFarmPartes = Yf
o.pararFarmPartes = KF
end
do
local P2 = 10
local LIXEIRO_TP = Vector3.new(-1216.14, 129.42, -5183.68)
local function getTeam() return LocalPlayer.Team and LocalPlayer.Team.Name or nil end
local function getLevel()
local ns = LocalPlayer:FindFirstChild("NewStats")
local lv = ns and ns:FindFirstChild("Level")
return lv and tonumber(lv.Value) or 0
end
local function getEmpregos()
local ok, mod = pcall(function() return require(ReplicatedStorage.Mercadinho.PrefModule) end)
if not ok or type(mod) ~= "table" or type(mod.Empregos) ~= "table" then return {} end
local list = {}
for name, data in pairs(mod.Empregos) do
table.insert(list, { nome = name, level = tonumber(data.Level) or 0, salario = tonumber(data.Salario) or 0 })
end
table.sort(list, function(a, b2)
if a.level == b2.level then return a.nome < b2.nome end
return a.level < b2.level
end)
return list
end
local function getLixoCount()
local inv = LocalPlayer:FindFirstChild("Inv")
local count = 0
if inv then
for _, v in ipairs(inv:GetChildren()) do
if v.Name == "Saco de Lixo" then count = count + 1 end
end
end
return count
end
local function getMoneySafe()
local ls = LocalPlayer:FindFirstChild("leaderstats")
local d = ls and ls:FindFirstChild("Dinheiro")
return d and tonumber(d.Value) or nil
end
local function getLixeiroEmp()
for _, e in ipairs(getEmpregos()) do
if string.lower(e.nome) == "lixeiro" then return e end
end
return { nome = "Lixeiro", level = 1, salario = 0 }
end
local function setFarmStatus(msg) o.garbageFarmStatus = tostring(msg or "") end
local function isRun(runId) return T.autoFarmLixeiroEnabled and not o.destroyed and o.garbageFarmRunId == runId end
local function wait2(t, runId)
local deadline = os.clock() + math.max(0, t or 0)
repeat
if not isRun(runId) then return false end
task.wait(math.min(0.1, math.max(0.01, deadline - os.clock())))
until os.clock() >= deadline
return isRun(runId)
end
local function sendJobSelect(remote, emp)
remote:FireServer(emp.nome, emp.level, true)
local pg = LocalPlayer:FindFirstChild("PlayerGui")
local lx = pg and pg:FindFirstChild("LixeiroUI")
local frame = lx and lx:FindFirstChild("LixeiroFrame")
if frame and frame.Visible then frame.Visible = false end
end
local function trySelectLixeiro(runId)
if getTeam() == "Lixeiro" then return true end
local merc = ReplicatedStorage:FindFirstChild("Mercadinho")
local remote = merc and merc:FindFirstChild("PrefRemote")
if not remote or not remote:IsA("RemoteEvent") then
setFarmStatus("PrefRemote nao encontrado")
return false
end
local emp = getLixeiroEmp()
if getLevel() < emp.level then return false end
setFarmStatus("Selecionando lixeiro...")
remote:FireServer(emp.nome, emp.level)
wait2(0.25, runId)
if not isRun(runId) then return false end
sendJobSelect(remote, emp)
local deadline = os.clock() + P2
repeat
if not isRun(runId) then return false end
if getTeam() == "Lixeiro" then
wait2(1.2, runId)
return true
end
task.wait(0.15)
until os.clock() >= deadline
return false
end
local function isLixoPart(part, prompt)
return part and part.Parent and part:IsA("BasePart") and part.Name == "Lixo"
and part.Transparency < 0.95
and (prompt and prompt.Parent == part and prompt:IsA("ProximityPrompt") and prompt.Enabled)
end
local function scanLixos()
local root = isRoot()
local folder = workspace:FindFirstChild("Lixeiro")
local list = {}
if not root or not folder then return list end
for _, part in ipairs(folder:GetChildren()) do
local prompt = part:FindFirstChild("PegarLixo")
if isLixoPart(part, prompt) then
table.insert(list, { parte = part, prompt = prompt, posicao = part.Position, distancia = (part.Position - root.Position).Magnitude })
end
end
table.sort(list, function(a, b3) return a.distancia < b3.distancia end)
return list
end
local function inRange(root, part, prompt)
if not root or not part or not part.Parent then return false end
local maxD = prompt and math.max(2.5, prompt.MaxActivationDistance - 0.65) or 4.25
return (root.Position - part.Position).Magnitude <= maxD
end
local function teleportTo(part, prompt, runId)
local root = isRoot()
if not root or not part or not part.Parent or not isRun(runId) then return false end
if inRange(root, part, prompt) then return true end
local position = prompt and o.interact.position(prompt) or part.Position
if not position then return false end
local above = math.min(part.Size.Y * 0.5 + 2.5, prompt and prompt.MaxActivationDistance * 0.6 or 3)
local moved, reason = o.travel.to("garbage", position + Vector3.new(0, above, 0), function()
return isRun(runId) and part.Parent ~= nil and (not prompt or prompt.Parent ~= nil)
end)
if not moved then setFarmStatus(reason or "TP interrompido"); return false end
return inRange(isRoot(), part, prompt)
end
local function firePrompt(prompt, force)
if not prompt or not prompt.Parent or not prompt.Enabled then return false end
if not force and type(fireproximityprompt) == "function" then
return pcall(fireproximityprompt, prompt, 0)
end
return pcall(function()
prompt:InputHoldBegin()
task.wait(math.max(0.15, prompt.HoldDuration + 0.1))
prompt:InputHoldEnd()
end)
end
local function collectOne(part, prompt, runId)
local before = getLixoCount()
for attempt = 1, 3 do
if not isRun(runId) or not isLixoPart(part, prompt) then
return not part.Parent or part.Transparency >= 0.95
end
local root = isRoot()
if not inRange(root, part, prompt) then return false end
firePrompt(prompt, attempt > 1)
local deadline = os.clock() + 2.75
repeat
if not isRun(runId) then return false end
if not part.Parent or part.Transparency >= 0.95 or getLixoCount() > before then return true end
task.wait(0.1)
until os.clock() >= deadline
end
return false
end
local function findDelivery()
local folder = workspace:FindFirstChild("EntregaLixo")
local lixeira = folder and folder:FindFirstChild("Lixeira")
local prompt = lixeira and lixeira:FindFirstChild("EntregarLixo")
if lixeira and lixeira:IsA("BasePart") and prompt and prompt:IsA("ProximityPrompt") then
return lixeira, prompt
end
return nil, nil
end
local function deliverLixos(count, runId)
local part, prompt = findDelivery()
if not part or not prompt then return false end
setFarmStatus(string.format("%d/%d coletados; indo entregar...", count, count))
if not teleportTo(part, prompt, runId) then
if not isRun(runId) then return false end
return false
end
local beforeCount = getLixoCount()
local beforeMoney = getMoneySafe()
for attempt = 1, 3 do
if not isRun(runId) then return false end
firePrompt(prompt, attempt > 1)
local deadline = os.clock() + 4
repeat
if not isRun(runId) then return false end
local nowCount = getLixoCount()
local nowMoney = getMoneySafe()
if (beforeCount > 0 and nowCount < beforeCount) or (beforeMoney and nowMoney and nowMoney > beforeMoney) then
return true
end
task.wait(0.15)
until os.clock() >= deadline
end
return false
end
function o.teleportLixeiro(active)
return o.travel.to("garbage", LIXEIRO_TP, active)
end
local function farmLoop(runId)
local deadline = os.clock() + 3
while o.travel.owner and isRun(runId) and os.clock() < deadline do task.wait(0.05) end
if not isRun(runId) then return end
setFarmStatus("TP para o ponto do lixeiro...")
local moved, reason = o.teleportLixeiro(function() return isRun(runId) end)
if not moved then
setFarmStatus(reason or "TP recusado")
T.autoFarmLixeiroEnabled = false
if o.lixeiroControl then o.lixeiroControl:Set(false, true) end
Xz(o.garbageFarmStatus)
return
end
while isRun(runId) do
if not trySelectLixeiro(runId) then
wait2(2, runId)
else
local goal = math.clamp(math.floor(tonumber(T.autoFarmLixeiroQuantidade) or 20), 1, 20)
local collected = 0
while isRun(runId) and getTeam() == "Lixeiro" and collected < goal do
local list = scanLixos()
if #list == 0 then
setFarmStatus(string.format("Coletados %d/%d; aguardando...", collected, goal))
wait2(1.25, runId)
else
local target = list[1]
if teleportTo(target.parte, target.prompt, runId) then
if collectOne(target.parte, target.prompt, runId) then
collected = collected + 1
wait2(0.45, runId)
else
if not isRun(runId) then break end
wait2(0.35, runId)
end
else
if not isRun(runId) then break end
wait2(0.4, runId)
end
end
end
if not isRun(runId) then break end
if getTeam() ~= "Lixeiro" then
setFarmStatus("Emprego alterado; retomando...")
elseif collected >= goal then
while isRun(runId) and not deliverLixos(collected, runId) do
wait2(1, runId)
end
if isRun(runId) then wait2(1.25, runId) end
end
end
end
end
local function startLixeiro(enable)
T.autoFarmLixeiroEnabled = enable == true
o.garbageFarmRunId = o.garbageFarmRunId + 1
local runId = o.garbageFarmRunId
o.travel.cancel("garbage")
if T.autoFarmLixeiroEnabled then
if T.autoFarmPumpkinEnabled and o.startPumpkin then o.startPumpkin(false) end
if o.stopAutoColeta then o.stopAutoColeta() end
if T.autoFarmPescaEnabled and o.startPesca then o.startPesca(false) end
if T.farmPartesEnabled and o.pararFarmPartes then o.pararFarmPartes() end
setFarmStatus("Verificando emprego...")
task.spawn(function()
local ok, err = pcall(farmLoop, runId)
if not ok and isRun(runId) then
setFarmStatus("Erro: " .. tostring(err))
T.autoFarmLixeiroEnabled = false
o.garbageFarmRunId = o.garbageFarmRunId + 1
if o.lixeiroControl then o.lixeiroControl:Set(false, true) end
end
end)
else
setFarmStatus("Auto Farm parado.")
o.travel.cancel("garbage")
end
end
o.startLixeiro = startLixeiro
end
do
local PESCA_TP = Vector3.new(-2593.61, 122.34, -3044.99)
local function normalize(text) return tostring(text or ""):lower():gsub("[^%w]", "") end
local function isRun(runId)
return T.autoFarmPescaEnabled and not o.destroyed and o.pescaRunId == runId
end
local function fishingPrompt(prompt)
local text = normalize(prompt.Name .. " " .. prompt.ActionText .. " " .. prompt.ObjectText)
return text:find("pesc", 1, true) or text:find("fishing", 1, true) or text:find("fish", 1, true)
end
local function findRod()
local containers = { LocalPlayer.Character, LocalPlayer:FindFirstChild("Backpack") }
for _, container in pairs(containers) do
for _, tool in ipairs(container:GetChildren()) do
if tool:IsA("Tool") then
local name = normalize(tool.Name)
if name:find("varadepesca", 1, true) or name == "vara" or name:find("fishingrod", 1, true)
or name:find("fishingpole", 1, true) then return tool end
end
end
end
end
local function farmLoop(session)
while isRun(session) do
local char, root = LocalPlayer.Character, isRoot()
if not root then task.wait(0.3) else
o.pescaStatus = "Indo ao ponto de pesca"
local moved, reason = o.travel.to("fishing", PESCA_TP, function() return isRun(session) end)
if not moved then
o.pescaStatus = reason or "Deslocamento interrompido"
if isRun(session) then Xz(o.pescaStatus) end
break
end
local prompts, nextScan, nextCast = {}, 0, 0
while isRun(session) and LocalPlayer.Character == char do
root = isRoot()
if not root or (root.Position - PESCA_TP).Magnitude > 8 then
o.pescaStatus = "Voce saiu do ponto; farm pausado"
break
end
if os.clock() >= nextScan then
nextScan, prompts = os.clock() + 2, {}
for _, prompt in ipairs(workspace:GetDescendants()) do
if prompt:IsA("ProximityPrompt") and prompt.Enabled and fishingPrompt(prompt) then
local position = o.interact.position(prompt)
if position and (position - root.Position).Magnitude <= prompt.MaxActivationDistance then
prompts[#prompts + 1] = prompt
end
end
end
end
local used = false
if not o.actionsBusy then
for _, prompt in ipairs(prompts) do
if not isRun(session) then break end
if prompt.Parent then
used = o.interact.prompt(prompt, function() return isRun(session) end) or used
end
end
if not used and os.clock() >= nextCast then
local rod, hum = findRod(), isCValid()
if rod and hum and rod.Enabled then
if rod.Parent ~= char then hum:EquipTool(rod); task.wait(0.15) end
if isRun(session) and rod.Parent == char then
rod:Activate()
nextCast, used = os.clock() + 1.5, true
end
end
end
end
o.pescaStatus = used and "Pescando" or "Aguardando vara ou interacao de pesca"
task.wait(0.35)
end
if LocalPlayer.Character == char then break end
end
end
if o.pescaRunId == session then
T.autoFarmPescaEnabled = false
if o.pescaControl then o.pescaControl:Set(false, true) end
end
end
function o.startPesca(enabled)
o.pescaRunId = o.pescaRunId + 1
T.autoFarmPescaEnabled = enabled == true
o.travel.cancel("fishing")
if o.pescaControl then o.pescaControl:Set(T.autoFarmPescaEnabled, true) end
if not T.autoFarmPescaEnabled then o.pescaStatus = "Parado"; return end
if T.autoFarmPumpkinEnabled and o.startPumpkin then o.startPumpkin(false) end
if o.stopAutoColeta then o.stopAutoColeta() end
if T.autoFarmLixeiroEnabled and o.startLixeiro then o.startLixeiro(false) end
local session = o.pescaRunId
task.spawn(function()
while o.travel.owner and isRun(session) do task.wait(0.05) end
local ok, reason = pcall(farmLoop, session)
if not ok and o.pescaRunId == session then
T.autoFarmPescaEnabled, o.pescaStatus = false, "Falha: " .. tostring(reason)
if o.pescaControl then o.pescaControl:Set(false, true) end
Xz(o.pescaStatus)
end
end)
end
end
do
local P = {
run = 0, alive = true, started = false, scanning = false, scanAt = -math.huge,
prompts = setmetatable({}, { __mode = "k" }),
retryAt = setmetatable({}, { __mode = "k" }),
}
o.pumpkinCollected, o.pumpkinStatus = 0, "Parado"
local completionAttributes = { "Collected", "Coletado", "Coletada", "Harvested", "Colhida", "IsCollected" }
local function status(message)
o.pumpkinStatus = tostring(message or "")
if o.pumpkinStatusLabel then
pcall(function()
o.pumpkinStatusLabel:Set(string.format("Aboboras: %d | %s", o.pumpkinCollected, o.pumpkinStatus), true)
end)
end
end
local function wanted()
return P.alive and not o.destroyed and (T.autoFarmPumpkinEnabled or T.pumpkinEspEnabled)
end
local function isPumpkin(text)
local name = tostring(text or ""):gsub("<.->", ""):lower():gsub("ó", "o"):gsub("Ó", "o"):gsub("ô", "o"):gsub("Ô", "o")
return name:find("abobora", 1, true) ~= nil or name:find("pumpkin", 1, true) ~= nil
end
local function nameFor(prompt)
if isPumpkin(prompt.ObjectText) then return prompt.ObjectText end
local parent = prompt.Parent
for _ = 1, 12 do
if not parent or parent == workspace then break end
if isPumpkin(parent.Name) then return parent.Name end
parent = parent.Parent
end
if isPumpkin(prompt.ActionText) then return "Abobora" end
end
local function partFor(prompt)
local parent = prompt.Parent
if parent and parent:IsA("Attachment") then parent = parent.Parent end
if not parent then return nil end
if parent:IsA("BasePart") then return parent end
if parent:IsA("Model") then
local primary = parent.PrimaryPart
if primary and primary.Parent and primary:IsDescendantOf(parent) then return primary end
return parent:FindFirstChildWhichIsA("BasePart", true)
end
return parent:FindFirstAncestorWhichIsA("BasePart")
end
local function track(obj)
if obj:IsA("ProximityPrompt") and not P.prompts[obj] then
P.prompts[obj] = { nameAt = -math.huge }
end
end
local function live(obj)
return obj and obj.Parent and obj:IsDescendantOf(workspace)
end
local function geometry(item)
local parts, visible, queue, head = {}, {}, { item }, 1
while head <= #queue and head <= 160 do
local obj = queue[head]; head = head + 1
if live(obj) then
if obj:IsA("BasePart") then
parts[#parts + 1] = obj
if (tonumber(obj.Transparency) or 0) < 0.98 then visible[#visible + 1] = obj end
end
for _, child in ipairs(obj:GetChildren()) do if #queue < 160 then queue[#queue + 1] = child end end
end
end
return visible, parts
end
local function itemFor(prompt, part)
local model = part and part:FindFirstAncestorWhichIsA("Model")
if not model or #model:GetChildren() > 24 or model:FindFirstChildOfClass("Humanoid") then return part end
local queue, head, count = { model }, 1, 0
while head <= #queue and head <= 120 do
local obj = queue[head]; head = head + 1
if obj:IsA("ProximityPrompt") then count = count + 1; if obj ~= prompt then return part end end
for _, child in ipairs(obj:GetChildren()) do
if #queue >= 120 then return part end
queue[#queue + 1] = child
end
end
return count == 1 and model or part
end
local function scan()
local queue, head, tail = { workspace }, 1, 1
local seen = setmetatable({}, { __mode = "k" })
while P.alive and not o.destroyed and head <= tail do
if not wanted() then task.wait(0.2) else
local processed, deadline = 0, os.clock() + 0.004
while head <= tail and processed < 100 and os.clock() < deadline do
local obj = queue[head]; queue[head] = nil; head = head + 1
processed = processed + 1
if obj and not seen[obj] and (obj == workspace or obj.Parent) then
seen[obj] = true
track(obj)
local ok, children = pcall(function() return obj:GetChildren() end)
if ok then
for i, child in ipairs(children) do
tail = tail + 1; queue[tail] = child
if i % 100 == 0 then
task.wait()
if not P.alive or o.destroyed then return end
end
end
end
end
end
task.wait(0.03)
end
end
P.scanning = false
end
local function ensureIndex(force)
if force then P.scanAt = -math.huge; P.scanRequested = true end
if P.scanning or (P.started and os.clock() - P.scanAt < 10) then return end
P.started, P.scanning, P.scanRequested = true, true, false
task.spawn(function()
local ok, reason = pcall(scan)
P.scanning, P.scanAt = false, P.scanRequested and -math.huge or os.clock()
if not ok and P.alive and not o.destroyed then
P.started, P.scanAt = false, os.clock() - 9
status("Falha na busca: " .. tostring(reason))
end
end)
end
handleReady(workspace.DescendantAdded:Connect(function(obj)
if P.alive then track(obj) end
end))
local function readCandidate(prompt, record, root, characters, readyOnly)
if not live(prompt) then return nil end
for _, char in ipairs(characters) do if prompt:IsDescendantOf(char) then return nil end end
if os.clock() >= record.nameAt or record.parent ~= prompt.Parent or record.objectText ~= prompt.ObjectText then
record.name, record.nameAt = nameFor(prompt), os.clock() + 1.5
record.parent, record.objectText = prompt.Parent, prompt.ObjectText
end
if not record.name then return nil end
local part = partFor(prompt)
if not live(part) then return nil end
local finished = record.completed
if finished then
local currentItem = itemFor(prompt, part)
local visible = geometry(currentItem)
local available = prompt.Enabled and prompt.MaxActivationDistance > 0 and (#finished.visuals == 0 or #visible > 0)
for _, key in ipairs(completionAttributes) do if currentItem:GetAttribute(key) == true then available = false end end
local replaced = currentItem ~= finished.item or (part ~= finished.part and not live(finished.part))
if not replaced and #visible > 0 and #finished.visuals > 0 then
local previousVisible, newVisible = false, false
for _, old in ipairs(finished.visuals) do
if live(old) and (tonumber(old.Transparency) or 0) < 0.98 then previousVisible = true; break end
end
for _, fresh in ipairs(visible) do if not table.find(finished.visuals, fresh) then newVisible = true; break end end
replaced = not previousVisible and newVisible
end
if replaced or (available and finished.unavailable) then record.completed = nil
elseif not available then finished.unavailable = true end
end
if record.completed then return nil end
if readyOnly and (not prompt.Enabled or prompt.MaxActivationDistance <= 0 or (P.retryAt[prompt] or 0) > os.clock()) then return nil end
local position = o.interact.position(prompt)
if not position then return nil end
return { prompt = prompt, parte = part, nome = record.name, posicao = position, distancia = (root.Position - position).Magnitude }
end
local function candidates(readyOnly)
if readyOnly == nil then readyOnly = true end
ensureIndex()
local root, out = isRoot(), {}
if not root then return out end
local characters = {}
for _, player in ipairs(Players:GetPlayers()) do
if player.Character then characters[#characters + 1] = player.Character end
end
for prompt, record in pairs(P.prompts) do
if not live(prompt) then
P.prompts[prompt], P.retryAt[prompt] = nil, nil
else
local ok, target = pcall(readCandidate, prompt, record, root, characters, readyOnly)
if ok and target then out[#out + 1] = target
elseif not ok then o.pumpkinLastError = tostring(target) end
end
end
table.sort(out, function(a, b) return a.distancia < b.distancia end)
return out
end
o.listPumpkins = candidates
local function refreshTarget(target)
if not live(target.item) then return end
local prompt = target.prompt
if not live(prompt) or not prompt:IsDescendantOf(target.item) then
local queue, head, fallback = { target.item }, 1, nil
while head <= #queue and head <= 160 do
local obj = queue[head]; head = head + 1
if obj:IsA("ProximityPrompt") then
fallback = fallback or obj
if obj.Enabled and obj.MaxActivationDistance > 0 then fallback = obj; break end
end
for _, child in ipairs(obj:GetChildren()) do if #queue < 160 then queue[#queue + 1] = child end end
end
if fallback and fallback ~= target.prompt then
target.prompt, target.nextAttempt = fallback, 0
track(fallback)
end
end
local part = live(target.prompt) and partFor(target.prompt)
if live(part) and (part == target.item or part:IsDescendantOf(target.item)) then target.parte = part end
if not live(target.parte) then
local visible, parts = geometry(target.item)
target.parte = visible[1] or parts[1] or target.parte
end
end
local function displayTarget()
local target, root = P.current, isRoot()
if target then refreshTarget(target) end
if target and root and live(target.parte) then
target.distancia = (root.Position - target.parte.Position).Magnitude
return target
end
return candidates(true)[1] or candidates(false)[1]
end
local function hideLabel()
if P.gui then P.gui.Enabled = false; P.gui.Adornee = nil end
end
local function showLabel(target)
if not target or not wanted() or not T.pumpkinEspEnabled then hideLabel(); return end
local parent = LocalPlayer:FindFirstChild("PlayerGui")
if not parent then hideLabel(); return end
if not P.gui or P.gui.Parent ~= parent or not P.label or P.label.Parent ~= P.gui then
if P.gui then pcall(function() P.gui:Destroy() end) end
local gui = Instance.new("BillboardGui")
gui.Name, gui.ResetOnSpawn, gui.AlwaysOnTop = "VeteranoPumpkinESP", false, true
gui.Size, gui.LightInfluence, gui.MaxDistance = UDim2.fromOffset(240, 42), 0, 100000
local label = Instance.new("TextLabel")
label.Size, label.BackgroundTransparency = UDim2.fromScale(1, 1), 1
label.Font, label.TextSize, label.TextWrapped = Enum.Font.GothamBold, 14, true
label.TextColor3, label.TextStrokeColor3 = Color3.fromRGB(255, 140, 30), Color3.fromRGB(0, 0, 0)
label.TextStrokeTransparency, label.Parent = 0.15, gui
gui.Parent = parent
P.gui, P.label = gui, label
end
P.gui.Adornee = target.parte
P.gui.StudsOffsetWorldSpace = Vector3.new(0, target.parte.Size.Y * 0.5 + 1.5, 0)
P.label.Text = string.format("%s\n%d m", target.nome, math.floor(target.distancia + 0.5))
P.label.Visible, P.label.TextTransparency, P.gui.AlwaysOnTop = true, 0, true
P.gui.Enabled = true
end
task.spawn(function()
while P.alive and not o.destroyed do
if wanted() then ensureIndex() end
if wanted() and T.pumpkinEspEnabled then
local ok, reason = pcall(function() showLabel(displayTarget()) end)
if not ok then o.pumpkinLastError = tostring(reason); hideLabel() end
else hideLabel() end
task.wait(0.25)
end
hideLabel()
end)
local function isRun(run)
return P.alive and not o.destroyed and T.autoFarmPumpkinEnabled and P.run == run
end
local function pumpkinProgress()
local best, native = 0, 0
local function amount(obj)
if obj:IsA("NumberValue") or obj:IsA("IntValue") then return math.max(0, obj.Value) end
if obj:IsA("BoolValue") then return obj.Value and 1 or 0 end
for _, key in ipairs({ "Quantity", "Quantidade", "Amount", "Count" }) do
local value = tonumber(obj:GetAttribute(key))
local child = obj:FindFirstChild(key)
if not value and child and (child:IsA("NumberValue") or child:IsA("IntValue")) then value = child.Value end
if value then return math.max(0, value) end
end
return 1
end
for _, root in pairs({ LocalPlayer.Character, LocalPlayer:FindFirstChild("Backpack") }) do
for _, obj in ipairs(root:GetChildren()) do
if obj:IsA("Tool") and isPumpkin(obj.Name) then native = native + 1 end
end
end
best = native
for _, name in ipairs({ "Inv", "Inventory", "Inventario", "Inventário", "Stats", "NewStats", "leaderstats", "Status", "Data", "PlayerData" }) do
local root = LocalPlayer:FindFirstChild(name)
if root then
local count = 0
for _, obj in ipairs(root:GetChildren()) do
if isPumpkin(obj.Name) or (obj:IsA("StringValue") and isPumpkin(obj.Value)) then count = count + amount(obj) end
end
for key, value in pairs(root:GetAttributes()) do
if isPumpkin(key) and type(value) == "number" then count = math.max(count, value) end
end
best = math.max(best, count)
end
end
for key, value in pairs(LocalPlayer:GetAttributes()) do
if isPumpkin(key) and type(value) == "number" then best = math.max(best, value) end
end
return best
end
local function prepare(target)
target.item = itemFor(target.prompt, target.parte)
local ok, progress = pcall(pumpkinProgress)
target.progress = ok and progress or nil
target.attributes = {}
for _, key in ipairs(completionAttributes) do target.attributes[key] = target.item:GetAttribute(key) end
target.visuals = geometry(target.item)
return target
end
local function consumed(target)
if not target.item.Parent or not target.item:IsDescendantOf(workspace) then return true end
if #target.visuals > 0 then
local visible = false
for _, part in ipairs(target.visuals) do
if part.Parent and part:IsDescendantOf(workspace) and (tonumber(part.Transparency) or 0) < 0.98 then visible = true; break end
end
if not visible then
local current = geometry(target.item)
if #current == 0 then return true end
target.visuals = current
refreshTarget(target)
end
end
for _, key in ipairs(completionAttributes) do
if target.attributes[key] ~= true and target.item:GetAttribute(key) == true then return true end
end
local ok, progress = pcall(pumpkinProgress)
if ok then
if target.progress == nil then target.progress = progress else return progress > target.progress end
end
return false
end
local function collect(target, run)
local char = LocalPlayer.Character
local function active()
local hum = isCValid()
return isRun(run) and LocalPlayer.Character == char and hum and hum.Health > 0
end
if not active() then return false, "Coleta cancelada." end
if consumed(target) then return true, nil, target.interacted == true or target.attempted == true end
local attempt, positioned = 0, false
target.nextAttempt, target.nextMoveAt = 0, 0
while active() do
refreshTarget(target)
if consumed(target) then return true, nil, target.interacted == true or target.attempted == true end
local prompt = target.prompt
local root = isRoot()
local position = live(prompt) and o.interact.position(prompt) or (live(target.parte) and target.parte.Position)
local reach = live(prompt) and math.max(1, prompt.MaxActivationDistance * 0.85) or 4
local inRange = root and position and (root.Position - position).Magnitude <= reach
if root and position and (not positioned or not inRange) and os.clock() >= target.nextMoveAt and not o.travel.owner then
local above = math.min(target.parte.Size.Y * 0.5 + 2.5, math.max(2, reach * 0.7))
status(positioned and "Corrigindo posicao na mesma abobora" or ("TP para " .. target.nome))
local moved, reason = o.travel.to("pumpkin", position + Vector3.new(0, above, 0), active)
target.nextMoveAt = os.clock() + 1.25
if not moved then
target.moveError = reason or "Tentando o TP novamente"
status(target.moveError)
else positioned = true; target.nextAttempt, target.moveError = 0, nil end
root = isRoot()
inRange = root and (root.Position - position).Magnitude <= reach
end
local harvesting = false
for _, key in ipairs({ "Collecting", "Coletando", "Harvesting", "InProgress" }) do
if target.item:GetAttribute(key) == true or (live(prompt) and prompt:GetAttribute(key) == true) then harvesting = true; break end
end
if live(prompt) and prompt.Enabled and inRange and not harvesting and os.clock() >= target.nextAttempt then
attempt = attempt + 1
status("Coletando " .. target.nome .. "; aguardando terminar")
target.attempted = true
local sent = o.interact.prompt(prompt, active, attempt % 2 == 0)
target.interacted = sent or target.interacted
target.nextAttempt = os.clock() + (sent and math.max(8, prompt.HoldDuration + 1) or 1)
elseif not inRange then status(target.moveError or "Recuperando alcance da mesma abobora")
elseif not live(prompt) or not prompt.Enabled then status("Aguardando interacao da mesma abobora")
else status("Aguardando concluir " .. target.nome) end
task.wait(0.15)
end
return false, "Coleta cancelada."
end
local function farmStep(run)
local hum = isCValid()
if not hum or hum.Health <= 0 or not isRoot() then
status("Aguardando personagem")
elseif o.travel.owner or o.collectionBusy then
status("Aguardando o TP atual")
else
local target = P.current
if not target then
target = candidates(true)[1]
if target then P.current = prepare(target) end
end
if target then
local character = LocalPlayer.Character
local ok, collected, reason, interacted = pcall(collect, target, run)
if not isRun(run) then return end
if character ~= LocalPlayer.Character then
P.retryAt[target.prompt] = nil
status("Retomando apos respawn")
elseif ok and collected then
if interacted then o.pumpkinCollected = o.pumpkinCollected + 1 end
P.retryAt[target.prompt] = os.clock() + 1
local record = P.prompts[target.prompt]
if record then record.completed = { item = target.item, part = target.parte, visuals = target.visuals } end
P.current = nil
status("Coleta confirmada")
else
status(ok and reason or ("Falha na mesma abobora: " .. tostring(collected)))
end
else status(P.scanning and "Buscando aboboras carregadas..." or "Aguardando abobora disponivel") end
end
end
local function farm(run)
while isRun(run) do
local ok, reason = pcall(farmStep, run)
if not ok and isRun(run) then
o.pumpkinLastError = tostring(reason)
status("Revalidando a mesma abobora apos uma falha")
ensureIndex(true)
end
task.wait(ok and 0.3 or 1)
end
end
function o.startPumpkin(enable)
if enable and T.autoFarmPumpkinEnabled and P.worker and P.worker.running then return end
P.run = P.run + 1
T.autoFarmPumpkinEnabled = enable == true and P.alive and not o.destroyed
o.travel.cancel("pumpkin")
if o.pumpkinControl then o.pumpkinControl:Set(T.autoFarmPumpkinEnabled, true) end
if not T.autoFarmPumpkinEnabled then
P.current = nil
status("Parado")
if not T.pumpkinEspEnabled then hideLabel() end
return
end
if o.stopAutoColeta then o.stopAutoColeta() end
if T.autoFarmLixeiroEnabled and o.startLixeiro then o.startLixeiro(false) end
if T.autoFarmPescaEnabled and o.startPesca then o.startPesca(false) end
if T.farmPartesEnabled and o.pararFarmPartes then o.pararFarmPartes() end
o.setPumpkinESP(true)
ensureIndex()
local run, worker = P.run, { running = true }
P.worker = worker
status("Buscando aboboras carregadas...")
task.spawn(function()
local ok, reason = pcall(farm, run)
worker.running = false
if not ok and isRun(run) then
o.startPumpkin(false)
status("Erro: " .. tostring(reason))
Xz(o.pumpkinStatus)
end
end)
end
function o.setPumpkinESP(enable)
T.pumpkinEspEnabled = enable == true and P.alive and not o.destroyed
if T.pumpkinEspEnabled then ensureIndex() elseif not T.autoFarmPumpkinEnabled then hideLabel() end
if o.pumpkinEspControl then o.pumpkinEspControl:Set(T.pumpkinEspEnabled, true) end
end
function o.refreshPumpkins()
for _, record in pairs(P.prompts) do record.nameAt = -math.huge end
if P.current then
P.current.nextAttempt, P.current.nextMoveAt = 0, 0
pcall(refreshTarget, P.current)
end
ensureIndex(true)
status("Atualizando aboboras; coleta em sequencia preservada")
end
function o.resetPumpkin()
o.startPumpkin(false)
o.setPumpkinESP(false)
o.pumpkinCollected = 0
P.retryAt = setmetatable({}, { __mode = "k" })
status("Parado")
end
function o.cleanupPumpkin()
o.resetPumpkin()
P.alive = false
if P.gui then P.gui:Destroy(); P.gui, P.label = nil, nil end
P.prompts, P.retryAt = {}, {}
end
end
do
local tracerColors = {
{ "Ciano", Color3.fromRGB(0, 255, 255) },
{ "Verde", Color3.fromRGB(60, 255, 90) },
{ "Rosa", Color3.fromRGB(255, 60, 190) },
{ "Roxo", Color3.fromRGB(160, 70, 255) },
{ "Vermelho", Color3.fromRGB(255, 45, 45) },
{ "Azul", Color3.fromRGB(70, 130, 255) },
{ "Laranja", Color3.fromRGB(255, 145, 30) },
{ "Branco", Color3.fromRGB(240, 240, 240) },
{ "Amarelo", Color3.fromRGB(255, 230, 60) },
{ "VerdeLima", Color3.fromRGB(180, 255, 0) },
{ "Magenta", Color3.fromRGB(255, 0, 200) },
{ "Turquesa", Color3.fromRGB(0, 200, 180) },
{ "Arco-iris", nil },
}
local corBalaOriginal = nil
local function getCorBala()
local cfg = LocalPlayer:FindFirstChild("OrpheusGunCFG")
local cb = cfg and cfg:FindFirstChild("CorBala")
if cb and corBalaOriginal == nil then corBalaOriginal = cb.Color end
return cb
end
local function aplicarCorBala()
local cor = getCorBala()
if not cor then return end
if not T.armaTracer then
if corBalaOriginal then pcall(function() cor.Color = corBalaOriginal end) end
return
end
if T.armaTracerCor == "Arco-iris" then
cor.Color = Color3.fromHSV((os.clock() * 0.25) % 1, 1, 1)
return
end
for _, g in ipairs(tracerColors) do
if g[1] == T.armaTracerCor and g[2] then
cor.Color = g[2]
return
end
end
end
o.restaurarArmas = function()
pcall(aplicarCorBala)
local cor = getCorBala()
if cor and corBalaOriginal then pcall(function() cor.Color = corBalaOriginal end) end
end
handleReady(RunService.RenderStepped:Connect(function()
if o.destroyed then return end
if not T.armaTracer then return end
pcall(aplicarCorBala)
end))
end
do
local originals = setmetatable({}, { __mode = "k" })
local generation, writing, elapsed = 0, false, 0
local lightingObjects = setmetatable({}, { __mode = "k" })
local bright = {
Brightness = 2, ClockTime = 14, GlobalShadows = false,
Ambient = Color3.fromRGB(255, 255, 255),
OutdoorAmbient = Color3.fromRGB(255, 255, 255),
FogStart = 0, FogEnd = 1000000, ExposureCompensation = 0,
}
local function desiredFor(obj)
local desired = {}
if obj == Lighting then
if T.fullbright then
for prop, value in pairs(bright) do desired[prop] = value end
elseif T.noLag then
desired.GlobalShadows = false
end
elseif obj:IsA("Atmosphere") and T.fullbright then
desired.Density, desired.Haze, desired.Glare = 0, 0, 0
elseif obj:IsA("PostEffect") then
if T.fullbright or T.noLag then desired.Enabled = false end
elseif T.noLag then
if obj:IsA("Terrain") then
desired.WaterWaveSize, desired.WaterWaveSpeed = 0, 0
desired.WaterReflectance, desired.Decoration = 0, false
elseif obj:IsA("BasePart") then
desired.CastShadow, desired.Reflectance = false, 0
desired.Material = Enum.Material.SmoothPlastic
elseif obj:IsA("ParticleEmitter") or obj:IsA("Trail") or obj:IsA("Beam")
or obj:IsA("Smoke") or obj:IsA("Fire") or obj:IsA("Sparkles") then
desired.Enabled = false
end
end
return desired
end
local function apply(obj)
if not obj.Parent and obj ~= Lighting then return end
local desired = desiredFor(obj)
if obj:IsA("PostEffect") or obj:IsA("Atmosphere") then lightingObjects[obj] = true end
local saved = originals[obj]
local wasWriting = writing
writing = true
if saved then
for prop, entry in pairs(saved) do
if desired[prop] == nil then
pcall(function() obj[prop] = entry.original end)
saved[prop] = nil
end
end
end
for prop, value in pairs(desired) do
local ok, current = pcall(function() return obj[prop] end)
if ok then
saved = saved or {}
if not saved[prop] then saved[prop] = { original = current } end
saved[prop].applied = value
if current ~= value then pcall(function() obj[prop] = value end) end
end
end
writing = wasWriting
originals[obj] = saved and next(saved) and saved or nil
end
local function refresh()
generation = generation + 1
local run = generation
apply(Lighting)
local objects, seen = {}, {}
local function add(obj)
if not seen[obj] then seen[obj] = true; objects[#objects + 1] = obj end
end
for obj in pairs(originals) do add(obj) end
if T.fullbright or T.noLag then
for _, obj in ipairs(Lighting:GetDescendants()) do add(obj) end
local cam = workspace.CurrentCamera
if cam then for _, obj in ipairs(cam:GetDescendants()) do add(obj) end end
end
if T.noLag then
for _, obj in ipairs(workspace:GetDescendants()) do add(obj) end
end
task.spawn(function()
for index, obj in ipairs(objects) do
if o.destroyed or generation ~= run then return end
pcall(apply, obj)
if index % 150 == 0 then task.wait() end
end
end)
end
function o.setFullbright(v)
T.fullbright = v == true
refresh()
end
function o.setNoLag(v)
T.noLag = v == true
refresh()
end
function o.restoreGraphics()
generation = generation + 1
T.fullbright, T.noLag = false, false
local objects = {}
for obj in pairs(originals) do objects[#objects + 1] = obj end
for _, obj in ipairs(objects) do pcall(apply, obj) end
end
handleReady(Lighting.Changed:Connect(function(prop)
if writing or o.destroyed or not (T.fullbright or T.noLag) then return end
local saved = originals[Lighting]
if saved and saved[prop] then
local ok, value = pcall(function() return Lighting[prop] end)
if ok and value ~= saved[prop].applied then saved[prop].original = value end
end
pcall(apply, Lighting)
end))
local function onAdded(obj)
if not o.destroyed and (T.fullbright or T.noLag) then
task.defer(function()
if not o.destroyed then pcall(apply, obj) end
end)
end
end
handleReady(workspace.DescendantAdded:Connect(onAdded))
handleReady(Lighting.DescendantAdded:Connect(onAdded))
handleReady(RunService.Heartbeat:Connect(function(dt)
if o.destroyed or not (T.fullbright or T.noLag) then return end
elapsed = elapsed + dt
if elapsed < 0.25 then return end
elapsed = 0
pcall(apply, Lighting)
for obj in pairs(lightingObjects) do if obj.Parent then pcall(apply, obj) end end
end))
end
local function Wz(dt)
if o.destroyed then return end
local hum = isCValid()
if hum then
if T.spoofWalkEnabled then
local ws = math.min(T.spoofWalkSpeed, 28)
if hum.WalkSpeed ~= ws then hum.WalkSpeed = ws end
end
end
pcall(o.updateJumpPower, hum)
if o.travelBusy then return end
pcall(Fz, dt)
if T.noclipEnabled then
pcall(Nz)
if not T.flyEnabled then pcall(ez) end
end
if T.flyEnabled or yz then pcall(xz, dt) end
end
handleReady(RunService.Heartbeat:Connect(Wz))
handleReady(RunService.Stepped:Connect(function()
if not o.destroyed then pcall(o.noclip.step) end
end))
do
local G = { records = {}, byCar = {}, byLabel = {}, selected = nil,
ids = setmetatable({}, { __mode = "k" }), nextId = 0, esp = {}, busy = false,
cancelToken = 0, actionToken = 0 }
o.garage = G
local elapsed, visualElapsed, optionKey = 0, 0, ""
local function active()
return not o.destroyed and (not G.busy or G.cancelToken == G.actionToken)
end
function G.cancel() G.cancelToken = G.cancelToken + 1 end
local function valid(record)
return record and record.car.Parent and record.seat.Parent and record.root.Parent
and record.car:IsDescendantOf(workspace) and record.seat:IsDescendantOf(record.car)
and record.root:IsDescendantOf(record.car)
end
function G.isEmpty(record)
if not valid(record) then return false end
for _, seat in ipairs(record.car:GetDescendants()) do
if (seat:IsA("Seat") or seat:IsA("VehicleSeat")) and seat.Occupant then return false end
end
return true
end
function G.isStopped(record)
if not valid(record) then return false end
local velocity = record.root.AssemblyLinearVelocity or Vector3.zero
local rotation = record.root.AssemblyAngularVelocity or Vector3.zero
return velocity.Magnitude <= 0.25 and rotation.Magnitude <= 0.15
end
function G.isEligible(record) return G.isEmpty(record) and G.isStopped(record) end
local function setStatus()
local record, root = G.selected, isRoot()
local text = "Selecione um carro carregado"
if valid(record) then
local distance = root and math.floor((record.root.Position - root.Position).Magnitude) or 0
text = record.label .. " | " .. distance .. " studs"
end
if G.status then pcall(function() G.status:Set(text, true) end) end
end
function G.select(record)
G.selected = valid(record) and record or nil
if G.dropdown then
pcall(function() G.dropdown:Set(G.selected and G.selected.label or nil, true) end)
end
setStatus()
end
function G.nearest()
local root, best, distance = isRoot(), nil, math.huge
if not root then return end
for _, record in ipairs(G.records) do
if valid(record) and G.isEligible(record) then
local d = (record.root.Position - root.Position).Magnitude
if d < distance then best, distance = record, d end
end
end
G.select(best)
if not best then Xz("Nenhum carro vazio e parado carregado por perto.") end
end
local function removeESP(car)
local entry = G.esp[car]
if not entry then return end
pcall(function() entry.billboard:Destroy() end)
pcall(function() entry.highlight:Destroy() end)
G.esp[car] = nil
end
function G.clearESP()
local cars = {}
for car in pairs(G.esp) do cars[#cars + 1] = car end
for _, car in ipairs(cars) do removeESP(car) end
end
function G.refresh()
local folder = workspace:FindFirstChild("CarrosSpawandos")
local records, byCar, byLabel, labels = {}, {}, {}, {}
if folder then
for _, seat in ipairs(folder:GetDescendants()) do
if seat:IsA("VehicleSeat") or (seat:IsA("Seat") and seat.Name == "DriveSeat") then
local node, car = seat.Parent, nil
while node and node ~= folder do
if node:IsA("Model") then car = node end
node = node.Parent
end
if node == folder and car and byCar[car] and seat.Name == "DriveSeat"
and byCar[car].seat.Name ~= "DriveSeat" then
local root = seat.AssemblyRootPart
if not root or not root:IsDescendantOf(car) then root = car.PrimaryPart or seat end
byCar[car].seat, byCar[car].root = seat, root
elseif node == folder and car and not byCar[car] then
if not G.ids[car] then G.nextId = G.nextId + 1; G.ids[car] = G.nextId end
local root = seat.AssemblyRootPart
if not root or not root:IsDescendantOf(car) then root = car.PrimaryPart or seat end
local record = { car = car, seat = seat, root = root,
label = string.format("[%03d] %s", G.ids[car], car.Name) }
byCar[car], byLabel[record.label] = record, record
records[#records + 1] = record
end
end
end
end
local filtered = {}
byCar, byLabel = {}, {}
for _, record in ipairs(records) do
if G.isEligible(record) then
filtered[#filtered + 1] = record
byCar[record.car], byLabel[record.label] = record, record
end
end
records = filtered
table.sort(records, function(a, b) return G.ids[a.car] < G.ids[b.car] end)
for _, record in ipairs(records) do labels[#labels + 1] = record.label end
local previous = G.selected and G.selected.car
G.records, G.byCar, G.byLabel = records, byCar, byLabel
G.selected = previous and byCar[previous] or nil
for car in pairs(G.esp) do if not byCar[car] then removeESP(car) end end
local key = table.concat(labels, "\n")
if G.dropdown and key ~= optionKey then
optionKey = key
G.dropdown:SetOptions(labels)
G.dropdown:Set(G.selected and G.selected.label or nil, true)
end
setStatus()
end
function G.current()
G.refresh()
local hum = isCValid()
local seat = hum and hum.SeatPart
if seat then
for _, record in ipairs(G.records) do
if seat:IsDescendantOf(record.car) then G.select(record); return end
end
end
Xz("Voce nao esta sentado em um carro carregado.")
end
local function target()
if not valid(G.selected) then return nil, "Selecione um carro na lista." end
if not G.isEligible(G.selected) then return nil, "O carro deixou de estar vazio ou parado; atualize a lista." end
return G.selected
end
local function moveCharacter(cf)
return o.teleportCharacter(cf)
end
local function leaveSeat(hum)
if not hum then return false end
if not hum.SeatPart then return true end
pcall(function()
local status = LocalPlayer:FindFirstChild("Status")
local belt = status and status:FindFirstChild("Cinto")
if belt then belt.Value = false end
end)
hum.Sit = false
pcall(function() hum:ChangeState(Enum.HumanoidStateType.GettingUp) end)
local deadline = os.clock() + 1
repeat
if not hum.SeatPart then return true end
task.wait(0.05)
until os.clock() >= deadline
return hum.SeatPart == nil
end
local function occupiedByOther(record, hum)
for _, obj in ipairs(record.car:GetDescendants()) do
if (obj:IsA("Seat") or obj:IsA("VehicleSeat")) and obj.Occupant and obj.Occupant ~= hum then
return true
end
end
return false
end
local function enter(record)
local hum = isCValid()
if not hum or hum.Health <= 0 then return false, "Personagem indisponivel." end
if not valid(record) then return false, "O carro saiu da area carregada." end
if record.seat.Occupant and record.seat.Occupant ~= hum then return false, "O motorista esta ocupado." end
if hum.SeatPart == record.seat then return true end
if not leaveSeat(hum) then return false, "Nao foi possivel sair do assento atual." end
if not active() then return false, "Acao interrompida." end
moveCharacter(record.seat.CFrame * CFrame.new(-4, 2, 0))
task.wait(0.12)
if not valid(record) or not active() then return false, "O carro nao esta disponivel." end
record.seat:Sit(hum)
local deadline = os.clock() + 1.6
repeat
if hum.SeatPart == record.seat then return true end
if not active() or not valid(record) then break end
task.wait(0.05)
until os.clock() >= deadline
return false, "O jogo recusou a entrada no carro. Verifique a trava e o assento."
end
local function controlled(record)
if record.root.Anchored then return false, "O carro esta ancorado." end
local hum = isCValid()
if occupiedByOther(record, hum) then return false, "O carro tem outro ocupante." end
if type(isnetworkowner) == "function" then
local ok, owned = pcall(isnetworkowner, record.root)
if ok then return owned == true, "O jogo nao concedeu controle fisico do carro." end
end
return hum and hum.SeatPart == record.seat, "Entre no carro antes de usar esta acao."
end
local function brake(record)
for _, part in ipairs(record.car:GetDescendants()) do
if part:IsA("BasePart") and not part.Anchored then
part.AssemblyLinearVelocity, part.AssemblyAngularVelocity = Vector3.zero, Vector3.zero
end
end
end
local function moveCar(record, cf)
brake(record)
record.car:PivotTo(cf)
brake(record)
end
local function spot(record, origin)
local box, size = record.car:GetBoundingBox()
local pivot = record.car:GetPivot()
local relative = pivot:ToObjectSpace(box)
local _, yaw = origin:ToOrientation()
local rotation = CFrame.Angles(0, yaw, 0)
local distance = math.max(size.X, size.Z) * 0.5 + 9
local ray = RaycastParams.new()
ray.FilterType = Enum.RaycastFilterType.Exclude
ray.FilterDescendantsInstances = { record.car, LocalPlayer.Character }
local overlap = OverlapParams.new()
overlap.FilterType = Enum.RaycastFilterType.Exclude
overlap.FilterDescendantsInstances = ray.FilterDescendantsInstances
for _, offset in ipairs({ Vector3.new(0, 0, -distance), Vector3.new(distance, 0, 0),
Vector3.new(-distance, 0, 0), Vector3.new(0, 0, distance) }) do
local position = origin:PointToWorldSpace(offset)
local floor = workspace:Raycast(position + Vector3.new(0, 30, 0), Vector3.new(0, -100, 0), ray)
if floor and floor.Normal.Y >= 0.75 then
local candidateBox = CFrame.new(position.X, floor.Position.Y + size.Y * 0.5 + 0.5, position.Z) * rotation
local blocked = false
for _, part in ipairs(workspace:GetPartBoundsInBox(candidateBox, size * 0.94, overlap)) do
if part.CanCollide then blocked = true; break end
end
if not blocked then return candidateBox * relative:Inverse() end
end
end
return nil
end
function G.teleport()
local record, msg = target()
if not record then return false, msg end
local hum = isCValid()
if not hum or not leaveSeat(hum) then return false, "Saia do assento antes de teleportar." end
if not valid(record) or not active() then return false, "O carro nao esta disponivel." end
moveCharacter(record.seat.CFrame * CFrame.new(-6, 2, 0))
return true, "Voce esta ao lado do carro selecionado."
end
function G.enter()
local record, msg = target()
if not record then return false, msg end
local root = isRoot()
local origin = root and root.CFrame
local ok, message = enter(record)
if not ok and origin and leaveSeat(isCValid()) then moveCharacter(origin) end
return ok, ok and "Entrada no carro confirmada." or message
end
function G.pull()
local record, reason = target()
if not record then return false, reason end
local root = isRoot()
if not root then return false, "Personagem indisponivel." end
if not G.isEligible(record) then return false, "Somente carros vazios e parados podem ser puxados." end
local destination = spot(record, root.CFrame)
if not destination then return false, "Nao ha espaco livre perto de voce." end
local moved, message = o.worldMove.move(record.car, destination, function()
return active() and valid(record) and G.isEmpty(record)
end)
if not moved then return false, message end
return true, "Carro trazido para perto de voce."
end
function G.unflip()
local record, msg = target()
if not record then return false, msg end
local owned, why = controlled(record)
if not owned then return false, why end
local pivot = record.car:GetPivot()
local _, yaw = pivot:ToOrientation()
moveCar(record, CFrame.new(pivot.Position + Vector3.new(0, 3, 0)) * CFrame.Angles(0, yaw, 0))
task.wait(0.35)
if not valid(record) or record.car:GetPivot().UpVector.Y < 0.7 then
return false, "O jogo nao confirmou a nova posicao do carro."
end
return true, "Carro colocado em pe."
end
function G.stop()
local record, msg = target()
if not record then return false, msg end
local owned, why = controlled(record)
if not owned then return false, why end
brake(record)
return true, "Carro freado."
end
function G.exit()
return leaveSeat(isCValid()), "Saida do assento solicitada."
end
function G.run(action)
if G.busy or o.destroyed then Xz("Aguarde a acao atual terminar."); return end
G.busy = true
G.actionToken = G.cancelToken
local token = G.actionToken
local belt = T.autoCintoEnabled
T.autoCintoEnabled = false
task.spawn(function()
local ok, success, msg = pcall(action)
G.busy = false
if not o.destroyed then
if token == G.cancelToken then T.autoCintoEnabled = belt end
Xz(ok and (msg or (success and "Acao concluida." or "Acao recusada."))
or ("Falha na acao: " .. tostring(success)))
end
end)
end
local function updateESP()
if not T.carEspEnabled then G.clearESP(); return end
local root = isRoot()
if not root then return end
for _, record in ipairs(G.records) do
if valid(record) then
local entry = G.esp[record.car]
if not entry then
local gui = Instance.new("BillboardGui")
gui.Name, gui.Adornee = "VT_CAR_ESP", record.root
gui.Size, gui.StudsOffsetWorldSpace = UDim2.fromOffset(250, 42), Vector3.new(0, 5, 0)
gui.AlwaysOnTop, gui.ResetOnSpawn = true, false
gui.Parent = LocalPlayer:FindFirstChild("PlayerGui")
local text = Instance.new("TextLabel")
text.Size, text.BackgroundTransparency = UDim2.fromScale(1, 1), 1
text.Font, text.TextSize, text.TextStrokeTransparency = Enum.Font.GothamBold, 13, 0.25
text.TextWrapped, text.Parent = true, gui
local highlight = Instance.new("Highlight")
highlight.Name, highlight.Adornee = "VT_CAR_HIGHLIGHT", record.car
highlight.DepthMode = Enum.HighlightDepthMode.AlwaysOnTop
highlight.FillTransparency, highlight.OutlineTransparency = 0.9, 0
highlight.Parent = record.car
entry = { billboard = gui, text = text, highlight = highlight }
G.esp[record.car] = entry
end
local distance = (record.root.Position - root.Position).Magnitude
local show = distance <= T.carEspDistance
local color = G.selected and G.selected.car == record.car
and Color3.fromRGB(60, 220, 255) or Color3.fromRGB(180, 100, 255)
entry.billboard.Enabled, entry.highlight.Enabled = show, show
entry.billboard.MaxDistance = T.carEspDistance
entry.text.Text, entry.text.TextColor3 = record.label .. "\n" .. math.floor(distance) .. " studs", color
entry.highlight.FillColor, entry.highlight.OutlineColor = color, color
end
end
end
function G.setESP(v)
T.carEspEnabled = v == true
if not T.carEspEnabled then G.clearESP() else G.refresh(); pcall(updateESP) end
end
function G.cleanup()
G.cancel()
T.carEspEnabled = false
G.clearESP()
end
handleReady(RunService.Heartbeat:Connect(function(dt)
if o.destroyed then return end
elapsed, visualElapsed = elapsed + dt, visualElapsed + dt
if elapsed >= 1.5 and not G.busy then elapsed = 0; pcall(G.refresh) end
if visualElapsed >= 0.15 then visualElapsed = 0; pcall(updateESP); setStatus() end
end))
end
do
local token = 0
local definitions = {
health = { label = "Vida", missing = "Tenha bandagem ou kit medico no inventario.",
items = { "bandagem", "bandage", "medkit", "kitmedico", "kitdeprimeirossocorros", "firstaid", "curativo", "medicamento" },
kinds = { "health", "heal", "cura", "medic", "medical" } },
thirst = { label = "Sede", missing = "Tenha agua ou bebida no inventario.",
items = { "agua", "water", "garrafadeagua", "suco", "juice", "refrigerante", "soda", "energetico", "bebida", "drink" },
kinds = { "thirst", "sede", "drink", "bebida", "hydratation" },
stats = { sede = true, thirst = true, hydration = true, hidratacao = true } },
hunger = { label = "Fome", missing = "Tenha comida no inventario.",
items = { "pao", "hamburguer", "hamburger", "burger", "sanduiche", "sandwich", "pizza", "marmita", "coxinha", "hotdog", "comida", "food", "lanche", "banana", "maca", "biscoito", "snack" },
kinds = { "hunger", "fome", "food", "comida" },
stats = { fome = true, hunger = true, food = true, alimentacao = true, saciedade = true } },
}
local function normalize(text)
text = tostring(text or ""):lower()
for from, to in pairs({ ["á"] = "a", ["à"] = "a", ["ã"] = "a", ["â"] = "a",
["é"] = "e", ["ê"] = "e", ["í"] = "i", ["ó"] = "o", ["ô"] = "o", ["õ"] = "o", ["ú"] = "u", ["ç"] = "c" }) do
text = text:gsub(from, to)
end
return text:gsub("[^%w]", "")
end
local function maximum(source, name)
local max = tonumber(source:GetAttribute("Max")) or tonumber(source:GetAttribute("Maximum"))
or tonumber(source:GetAttribute("MaxValue"))
if not max and source.Parent then
local sibling = source.Parent:FindFirstChild("Max" .. name) or source.Parent:FindFirstChild(name .. "Max")
if sibling and (sibling:IsA("NumberValue") or sibling:IsA("IntValue")) then max = sibling.Value end
max = max or tonumber(source.Parent:GetAttribute("Max" .. name))
end
if source:GetAttribute("Normalized") == true then max = 1 end
return max and max > 0 and max or 100
end
local function readStat(kind)
if kind == "health" then
local hum = isCValid()
if hum then return hum.Health, hum.MaxHealth end
return nil
end
local aliases = definitions[kind].stats
local containers = { LocalPlayer }
local char = LocalPlayer.Character
if char then containers[#containers + 1] = char end
for _, name in ipairs({ "Stats", "Stats", "Data", "PlayerData", "leaderstats" }) do
local container = LocalPlayer:FindFirstChild(name)
if container then containers[#containers + 1] = container end
end
for _, container in ipairs(containers) do
for name, value in pairs(container:GetAttributes()) do
if aliases[normalize(name)] and type(value) == "number" then
local max = tonumber(container:GetAttribute("Max" .. name)) or tonumber(container:GetAttribute(name .. "Max")) or 100
return value, max
end
end
if container ~= LocalPlayer and container ~= char then
for _, value in ipairs(container:GetDescendants()) do
if (value:IsA("NumberValue") or value:IsA("IntValue")) and aliases[normalize(value.Name)] then
return value.Value, maximum(value, value.Name)
end
end
end
end
end
local function score(tool, kind)
local definition = definitions[kind]
for _, attribute in ipairs({ "ConsumableType", "ItemType", "Category", "Type" }) do
local value = normalize(tool:GetAttribute(attribute))
for _, expected in ipairs(definition.kinds) do if value == expected then return 3 end end
end
local name = normalize(tool.Name)
for _, expected in ipairs(definition.items) do
if name == expected then return 2 end
if #expected >= 4 and name:find(expected, 1, true) then return 1 end
end
return 0
end
local function findTool(kind)
local best, bestScore = nil, 0
for _, container in pairs({ LocalPlayer.Character, LocalPlayer:FindFirstChild("Backpack") }) do
for _, tool in ipairs(container:GetChildren()) do
if tool:IsA("Tool") and tool.Enabled then
local value = score(tool, kind)
if value > bestScore then best, bestScore = tool, value end
end
end
end
return best
end
local function full(value, max) return value and max and max > 0 and value >= max * 0.995 end
local function consume(kind, run)
local char, hum = LocalPlayer.Character, isCValid()
if not char or not hum or hum.Health <= 0 then return false, "Personagem indisponivel." end
local function active() return token == run and not o.destroyed and LocalPlayer.Character == char and hum.Health > 0 end
local used, deadline = 0, os.clock() + 65
while active() and os.clock() < deadline and used < 25 do
local value, max = readStat(kind)
if full(value, max) then return true, definitions[kind].label .. " preenchida." end
local tool = findTool(kind)
if not tool then
return false, used == 0 and definitions[kind].missing or "Os consumiveis acabaram antes de completar a barra."
end
if tool.Parent ~= char then hum:EquipTool(tool); task.wait(0.2) end
if not active() then break end
if tool.Parent ~= char then return false, "O jogo nao equipou o consumivel." end
local sent, reason = pcall(function() tool:Activate() end)
if not sent then return false, "Nao foi possivel usar o item: " .. tostring(reason) end
used = used + 1
local changed, endTime = false, os.clock() + 6
repeat
task.wait(0.1)
if not active() then break end
local nextValue = readStat(kind)
if value and nextValue and nextValue > value + 0.001 then changed = true; break end
if not tool.Parent then break end
until os.clock() >= endTime
if tool.Parent then pcall(function() tool:Deactivate() end) end
if not active() then break end
local after, maximumValue = readStat(kind)
if full(after, maximumValue) then return true, definitions[kind].label .. " preenchida." end
if not value then return false, "Uso solicitado; a barra nao esta disponivel para confirmar o consumo." end
if not changed and after and after <= value then
return false, "O item nao alterou a barra. Ele pode exigir uma interacao propria do jogo."
end
task.wait(0.6)
end
return false, active() and "Preenchimento interrompido por limite de tentativas." or "Uso de consumiveis cancelado."
end
function o.cancelQuickActions() token = token + 1 end
function o.useConsumable(kind)
if not definitions[kind] then return false end
if o.actionsBusy then Xz("Aguarde o consumo atual terminar."); return false end
token, o.actionsBusy = token + 1, true
local run, char, hum = token, LocalPlayer.Character, isCValid()
local previous = char and char:FindFirstChildOfClass("Tool")
task.spawn(function()
local ok, success, message = pcall(consume, kind, run)
if LocalPlayer.Character == char and hum and hum.Parent and hum.Health > 0 then
if previous and previous.Parent and previous.Parent ~= char then pcall(function() hum:EquipTool(previous) end) end
end
o.actionsBusy = false
if not o.destroyed then Xz(ok and message or ("Falha ao usar consumivel: " .. tostring(success))) end
end)
return true
end
o.readNeed = readStat
end
local W = S:CreateWindow({
Name = "VETERANO TEAM",
Subtitle = "by @VETERANO.DEV | SINTONIA RP",
Icon = "crown",
Logo = true,
Size = UDim2.fromOffset(420, 300),
MinSize = Vector2.new(420, 300),
ToggleKey = Enum.KeyCode.K,
})
do
local setVisible = W.SetVisible
local firstOpen = true
function W:SetVisible(state)
if state ~= false then
local root = self.root
if firstOpen then
root.Size = UDim2.fromOffset(420, 300)
firstOpen = false
end
root.AnchorPoint = Vector2.new(0.5, 0.5)
local camera = workspace.CurrentCamera
if camera then
local screen = camera.ViewportSize
local ok, inset = pcall(function() return GuiService:GetGuiInset() end)
if ok and typeof(inset) == "Vector2" then screen = screen + inset end
root.Position = UDim2.fromOffset(math.round(screen.X * 0.5), math.round(screen.Y * 0.5))
end
end
return setVisible(self, state)
end
W:SetVisible(false)
end
pcall(function() W:SetBackdrop("aurora") end)
pcall(function()
local cc = W.root:FindFirstChildOfClass("UICorner")
if cc then cc.CornerRadius = UDim.new(0, 10) end
local ss = W.root:FindFirstChildOfClass("UIStroke")
if ss then ss.Color = TEMA_ROXO.Accent; ss.Thickness = 1.5 end
end)
local tabPlayer = W:CreateTab({ Name = "PLAYER", Icon = "user" })
tabPlayer:SetColumns(2)
local secP = tabPlayer:CreateSection({ Name = "Movimento" })
secP:Toggle({ Name = "WalkSpeed", Default = false, Callback = function(v) T.spoofWalkEnabled = v end })
secP:Slider({ Name = "Velocidade", Min = 16, Max = 250, Default = 40, Callback = function(v) T.spoofWalkSpeed = v end })
secP:Toggle({ Name = "JumpPower", Default = false, Callback = function(v) o.setJumpPower(v) end })
secP:Slider({ Name = "Forca Jump", Min = 50, Max = 150, Default = 90, Callback = function(v) T.spoofJumpPower = v; o.updateJumpPower(isCValid()) end })
local secPM = tabPlayer:CreateSection({ Name = "Movimento Extra" })
secPM:Toggle({ Name = "Noclip", Default = false, Callback = function(v) T.noclipEnabled = v; pcall(Nz) end })
secPM:Toggle({ Name = "Fly", Default = false, Callback = function(v) T.flyEnabled = v end })
secPM:Slider({ Name = "Vel. Fly", Min = 16, Max = 100, Default = 60, Callback = function(v) T.flySpeed = math.clamp(v, 16, 100) end })
secPM:Toggle({ Name = "Ghost Mode", Default = false, Callback = function(v) T.ghostEnabled = v; if v then pcall(o.startGhost) else pcall(o.stopGhost) end end })
local secPW = tabPlayer:CreateSection({ Name = "Mundo" })
secPW:Toggle({ Name = "Fullbright", Default = false, Callback = function(v) o.setFullbright(v) end })
secPW:Toggle({ Name = "No Lag", Default = false, Callback = function(v) o.setNoLag(v) end })
local tabESP = W:CreateTab({ Name = "ESP", Icon = "eye" })
tabESP:SetColumns(2)
local secE = tabESP:CreateSection({ Name = "ESP" })
secE:Toggle({ Name = "Ativar ESP", Default = false, Callback = function(v) T.espEnabled = v; if not v then pcall(PF_all) end end })
secE:Slider({ Name = "Distancia Max", Min = 100, Max = 5000, Default = 1500, Callback = function(v) T.maxDistance = v end })
local secEV = tabESP:CreateSection({ Name = "Visual" })
secEV:Toggle({ Name = "Caixa 2D", Default = false, Callback = function(v) T.box = v end })
secEV:Toggle({ Name = "Caixa 3D", Default = false, Callback = function(v) T.box3d = v end })
secEV:Toggle({ Name = "Nome", Default = false, Callback = function(v) T.name = v end })
secEV:Toggle({ Name = "Distancia", Default = false, Callback = function(v) T.distance = v end })
secEV:Toggle({ Name = "Item na mao", Default = false, Callback = function(v) T.heldItem = v end })
secEV:Toggle({ Name = "Esqueleto", Default = false, Callback = function(v) T.skeleton = v end })
secEV:Toggle({ Name = "Highlight", Default = false, Callback = function(v) T.highlight = v end })
secEV:Toggle({ Name = "Tracer", Default = false, Callback = function(v) T.tracer = v end })
secEV:Toggle({ Name = "Seta fora da tela", Default = false, Callback = function(v) T.arrow = v end })
secEV:Toggle({ Name = "Times", Default = false, Callback = function(v) T.teamTag = v end })
local tabAim = W:CreateTab({ Name = "AIMBOT", Icon = "crosshair" })
tabAim:SetColumns(2)
local secA = tabAim:CreateSection({ Name = "Aimbot" })
secA:Toggle({ Name = "Ativar Aimbot", Default = false, Callback = function(v) T.aimbotEnabled = v; if v then pcall(aimbotLoop) else pcall(stopAimbot) end end })
secA:Dropdown({ Name = "Modo", Options = { "Legit", "Rage" }, Default = "Legit", Callback = function(v) T.aimbotMode = v end })
secA:Slider({ Name = "FOV", Min = 10, Max = 600, Default = 120, Callback = function(v) T.aimbotFov = v end })
secA:Slider({ Name = "Distancia", Min = 50, Max = 1000, Default = 500, Callback = function(v) T.aimbotDistance = v end })
secA:Slider({ Name = "Suavidade", Min = 1, Max = 100, Default = 15, Callback = function(v) T.aimbotSmoothness = v / 100 end })
secA:Dropdown({ Name = "Parte corpo", Options = { "Cabeca", "Tronco" }, Default = "Cabeca", Callback = function(v) T.aimbotBodyPart = v end })
local secAF = tabAim:CreateSection({ Name = "Filtros" })
secAF:Toggle({ Name = "Wall Check", Default = true, Callback = function(v) T.aimbotWallCheck = v end })
secAF:Toggle({ Name = "Friend Check", Default = true, Callback = function(v) T.friendCheck = v end })
secAF:Toggle({ Name = "Staff Check", Default = true, Callback = function(v) T.adminCheck = v end })
local tabWpn = W:CreateTab({ Name = "WEAPONS", Icon = "zap" })
tabWpn:SetColumns(2)
local secWT = tabWpn:CreateSection({ Name = "Tracer" })
secWT:Toggle({ Name = "Tracer Neon", Default = false, Callback = function(v) T.armaTracer = v end })
secWT:Dropdown({ Name = "Cor", Options = { "Ciano", "Verde", "Rosa", "Roxo", "Vermelho", "Azul", "Laranja", "Branco", "Amarelo", "Arco-iris" }, Default = "Ciano", Callback = function(v) T.armaTracerCor = v end })
local secWC = tabWpn:CreateSection({ Name = "Coletar Itens" })
o.coletaStatusLabel = secWC:Label({ Name = "Aguardando coleta" })
o.retornoControl = secWC:Toggle({ Name = "Retornar ao pekar arma", Default = true, Callback = function(v) T.returnOnWeaponPickup = v end })
o.coletaControl = secWC:Toggle({ Name = "Auto Coleta", Default = false, Callback = function(v)
if v then pcall(o.startAutoColeta) else pcall(o.stopAutoColeta) end
end })
secWC:Toggle({ Name = "Puxar arma para coletar", Default = false, Callback = function(v) T.pullWeaponsEnabled = v end })
secWC:Label({ Name = "Coleta e retorno por TP direto" })
secWC:Button({ Name = "Puxar arma mais proxima", Callback = function()
task.spawn(function()
local drops = o.listDrops()
if not drops[1] then Xz("Nenhuma arma dropada carregada."); return end
local ok, success, message = pcall(o.coletarItem, drops[1], nil, true)
if not ok or not success then Xz(ok and message or tostring(success)) end
end)
end })
secWC:Slider({ Name = "Intervalo de busca (ms)", Min = 100, Max = 2000, Default = 350, Callback = function(v) T.autoColetaCooldown = v / 1000 end })
secWC:Toggle({ Name = "HUD de itens", Default = false, Callback = function(v)
T.coletaHudEnabled = v
if o.setColetaHudVisible then o.setColetaHudVisible(v) end
end })
local tabMisc = W:CreateTab({ Name = "MISC", Icon = "settings" })
tabMisc:SetColumns(2)
local secME = tabMisc:CreateSection({ Name = "Extras" })
secME:Toggle({ Name = "Auto Lockpick Completo", Default = false, Callback = function(v) o.setPerfectLockpick(v) end })
secME:Toggle({ Name = "Camuflar Nick", Default = false, Callback = function(v) if o.setCamuflarNick then o.setCamuflarNick(v) end end })
local secMF = tabMisc:CreateSection({ Name = "Auto Farm" })
secMF:Toggle({ Name = "Farm Partes", Default = false, Callback = function(v)
if v then pcall(o.iniciarFarmPartes) else pcall(o.pararFarmPartes) end
end })
o.lixeiroControl = secMF:Toggle({ Name = "Farm Lixeiro", Default = false, Callback = function(v) if o.startLixeiro then o.startLixeiro(v) end end })
secMF:Button({ Name = "TP Lixeiro", Callback = function()
task.spawn(function()
local ok, success, reason = pcall(o.teleportLixeiro)
if not ok or not success then Xz(ok and (reason or "TP recusado") or tostring(success)) end
end)
end })
secMF:Slider({ Name = "Lixos por entrega", Min = 1, Max = 20, Default = 20, Callback = function(v) T.autoFarmLixeiroQuantidade = v end })
o.pescaControl = secMF:Toggle({ Name = "Farm Pesca", Default = false, Callback = function(v) if o.startPesca then o.startPesca(v) end end })
o.pumpkinControl = secMF:Toggle({ Name = "Farm Aboboras", Default = false, Callback = function(v) o.startPumpkin(v) end })
o.pumpkinEspControl = secMF:Toggle({ Name = "ESP Aboboras", Default = false, Callback = function(v) o.setPumpkinESP(v) end })
o.pumpkinStatusLabel = secMF:Label({ Name = "Aboboras: 0 | Parado" })
secMF:Button({ Name = "Atualizar Aboboras", Callback = function() o.refreshPumpkins() end })
local secMA = tabMisc:CreateSection({ Name = "Acoes" })
secMA:Button({ Name = "Curar", Callback = function() o.useConsumable("health") end })
secMA:Button({ Name = "Encher Sede", Callback = function() o.useConsumable("thirst") end })
secMA:Button({ Name = "Encher Fome", Callback = function() o.useConsumable("hunger") end })
local tabCar = W:CreateTab({ Name = "VEICULO", Icon = "car" })
tabCar:SetColumns(2)
local secC = tabCar:CreateSection({ Name = "Veiculo" })
secC:Toggle({ Name = "Durabilidade HUD", Default = false, Callback = function(v)
T.durabilidadeHudEnabled = v
if v then pcall(o.ativarDurabilidade) else pcall(o.desativarDurabilidade) end
end })
secC:Toggle({ Name = "Cinto Auto", Default = false, Callback = function(v) T.autoCintoEnabled = v end })
local secCM = tabCar:CreateSection({ Name = "Movimento" })
secCM:Toggle({ Name = "Noclip Car", Default = false, Callback = function(v)
T.noclipCarEnabled = v
if o.aplicarNoclipCar then pcall(o.aplicarNoclipCar) end
end })
secCM:Toggle({ Name = "VFly", Default = false, Callback = function(v)
T.vFlyEnabled = v
if o.aplicarNoclipCar then pcall(o.aplicarNoclipCar) end
end })
secCM:Slider({ Name = "VFly Horiz", Min = 20, Max = 500, Default = 60, Callback = function(v) T.vFlyHorizontalSpeed = v end })
secCM:Slider({ Name = "VFly Vert", Min = 10, Max = 300, Default = 30, Callback = function(v) T.vFlyVerticalSpeed = v end })
secCM:Slider({ Name = "Boost %", Min = 100, Max = 500, Default = 100, Callback = function(v)
T.boostFactor = v / 100
local hum = isCValid()
if hum then pcall(hz, hum) end
end })
do
local tabGar = W:CreateTab({ Name = "GARAGEM", Icon = "car-front" })
tabGar:SetColumns(2)
local G = o.garage
local list = tabGar:CreateSection({ Name = "Selecionar Veiculo" })
G.dropdown = list:Dropdown({ Name = "Carro", Options = {}, Search = true, Placeholder = "Selecione",
Callback = function(label) G.select(G.byLabel[label]) end })
G.status = list:Label({ Name = "Somente carros vazios e parados" })
list:Button({ Name = "Atualizar Lista", Callback = function() G.refresh(); Xz(#G.records .. " carro(s) vazio(s) e parado(s).") end })
list:Button({ Name = "Mais Proximo Vazio/Parado", Callback = function() G.refresh(); G.nearest() end })
local visuals = tabGar:CreateSection({ Name = "ESP Car" })
visuals:Toggle({ Name = "ESP dos Carros", Default = false, Callback = function(v) G.setESP(v) end })
visuals:Slider({ Name = "Distancia ESP Car", Min = 100, Max = 5000, Default = 1500,
Callback = function(v) T.carEspDistance = v end })
local actions = tabGar:CreateSection({ Name = "Interagir com o Selecionado" })
actions:Button({ Name = "TP ate o Carro", Callback = function() G.run(G.teleport) end })
actions:Button({ Name = "Entrar no Carro", Callback = function() G.run(G.enter) end })
actions:Button({ Name = "Puxar Carro ate Mim", Callback = function() G.run(G.pull) end })
actions:Button({ Name = "Desvirar Carro", Callback = function() G.run(G.unflip) end })
actions:Button({ Name = "Frear Carro", Callback = function() G.run(G.stop) end })
actions:Button({ Name = "Sair do Carro", Callback = function() G.run(G.exit) end })
local current = tabGar:CreateSection({ Name = "Carro Atual" })
current:Button({ Name = "Reparar Carro", Callback = function()
local car = rz()
if not car then Xz("Voce nao esta em um carro."); return end
local vida = o.vidaDoCarro and o.vidaDoCarro(car)
if vida and (vida:IsA("NumberValue") or vida:IsA("IntValue")) then
vida.Value = math.max(vida.Value, 1500)
Xz("Vida do carro atualizada.")
else Xz("Vida do carro nao encontrada.") end
end })
G.refresh()
end
local tabCfg = W:CreateTab({ Name = "CONFIG", Icon = "wrench" })
tabCfg:SetColumns(2)
local secCfgS = tabCfg:CreateSection({ Name = "Sobre" })
secCfgS:Label({ Text = "VETERANO TEAM\nBuild: slate-roxo-v2-tp-direto\nDev: @VETERANO.DEV\n\nK = menu | F = fly | N = noclip" })
local secCfgA = tabCfg:CreateSection({ Name = "Acoes" })
secCfgA:Button({ Name = "Resetar Menu", Callback = function()
for k, v in pairs(T) do
if type(v) == "boolean" then T[k] = false end
end
if o.travel then o.travel.cancel() end
if o.cancelQuickActions then o.cancelQuickActions() end
if o.noclip then o.noclip.restore() end
if o.retornoControl then o.retornoControl:Set(false, true) end
T.spoofWalkSpeed = 40
T.spoofJumpPower = 90
T.flySpeed = 60
T.maxDistance = P
T.aimbotFov = 120
T.aimbotDistance = 500
T.aimbotSmoothness = 0.15
T.vFlyHorizontalSpeed = 60
T.vFlyVerticalSpeed = 30
T.autoColetaCooldown = 0.35
T.autoFarmLixeiroQuantidade = 20
if o.resetPumpkin then pcall(o.resetPumpkin) end
T.boostFactor = 1
T.carEspDistance = 1500
if o.restoreJumpPower then pcall(o.restoreJumpPower) end
if o.restoreGraphics then pcall(o.restoreGraphics) end
if o.setPerfectLockpick then pcall(o.setPerfectLockpick, false) end
if o.garage then pcall(o.garage.cancel); pcall(o.garage.setESP, false) end
if o.stopAutoColeta then pcall(o.stopAutoColeta) end
if o.pararFarmPartes then pcall(o.pararFarmPartes) end
if o.startLixeiro then pcall(o.startLixeiro, false) end
if o.startPesca then pcall(o.startPesca, false) end
if o.stopGhost then pcall(o.stopGhost) end
if o.setCamuflarNick then pcall(o.setCamuflarNick, false) end
if o.restaurarNoclipCar then pcall(o.restaurarNoclipCar) end
if o.setColetaHudVisible then pcall(o.setColetaHudVisible, false) end
if o.stopAimbot then pcall(stopAimbot) end
if PF_all then pcall(PF_all) end
pcall(Nz)
Xz("Menu resetado")
end })
secCfgA:Button({ Name = "Rejoin (Mesmo servidor)", Callback = function()
Xz("Rejoinando...")
task.spawn(function()
pcall(function()
if #Players:GetPlayers() <= 1 or game.JobId == "" then
TeleportService:Teleport(game.PlaceId, LocalPlayer)
else
TeleportService:TeleportToPlaceInstance(game.PlaceId, game.JobId, LocalPlayer)
end
end)
end)
end })
secCfgA:Button({ Name = "Server Hop", Callback = function()
Xz("Procurando servidor...")
task.spawn(function()
local ok, body = pcall(function()
return game:HttpGet(string.format(
"https://games.roblox.com/v1/games/%d/servers/Public?sortOrder=Desc&limit=100&excludeFullGames=true",
game.PlaceId))
end)
if not ok or type(body) ~= "string" then Xz("Falha ao buscar servidores"); return end
local dec, data = pcall(function() return HttpService:JSONDecode(body) end)
if not dec or not data or not data.data then Xz("Lista invalida"); return end
local candidatos = {}
for _, s in ipairs(data.data) do
if s.id ~= game.JobId and s.playing < s.maxPlayers then
table.insert(candidatos, s)
end
end
if #candidatos == 0 then Xz("Sem servidores abertos"); return end
local alvo = candidatos[math.random(1, #candidatos)]
pcall(function()
TeleportService:TeleportToPlaceInstance(game.PlaceId, alvo.id, LocalPlayer)
end)
end)
end })
secCfgA:Button({ Name = "Fechar Menu", Callback = function()
pcall(function() W:SetVisible(false) end)
Xz("K para reabrir")
end })
handleReady(UserInputService.InputBegan:Connect(function(input, gp)
if gp or o.destroyed then return end
if input.KeyCode == T.flyKeybind then
T.flyEnabled = not T.flyEnabled
Xz(T.flyEnabled and "Fly ON" or "Fly OFF")
elseif input.KeyCode == T.noclipKeybind then
T.noclipEnabled = not T.noclipEnabled
pcall(Nz)
Xz(T.noclipEnabled and "Noclip ON" or "Noclip OFF")
end
end))
C.__VETERANO_SINTONIA_WINDOW = W
C.__VETERANO_SINTONIA_BUILD = "slate-roxo-v2-tp-direto"
C.__VETERANO_SINTONIA_CLEANUP = function()
o.destroyed = true
if o.travel then o.travel.cancel() end
if o.cancelQuickActions then o.cancelQuickActions() end
if o.noclip then o.noclip.restore() end
if o.restoreJumpPower then pcall(o.restoreJumpPower) end
if o.restoreGraphics then pcall(o.restoreGraphics) end
if o.setPerfectLockpick then pcall(o.setPerfectLockpick, false) end
if o.garage then pcall(o.garage.cleanup) end
C.__VETERANO_SINTONIA_RUNTIME = nil
T.autoColetaEnabled = false
if o.cleanupPumpkin then pcall(o.cleanupPumpkin) end
if o.cleanupCollection then pcall(o.cleanupCollection) end
if o.pararFarmPartes then pcall(o.pararFarmPartes) end
if o.startLixeiro then pcall(o.startLixeiro, false) end
if o.startPesca then pcall(o.startPesca, false) end
if o.stopGhost then pcall(o.stopGhost) end
if o.setCamuflarNick then pcall(o.setCamuflarNick, false) end
if o.restaurarNoclipCar then pcall(o.restaurarNoclipCar) end
if o.stopAimbot then pcall(stopAimbot) end
if PF_all then pcall(PF_all) end
if o.coletaHudGui then pcall(function() o.coletaHudGui:Destroy() end) end
if o.destruirDurabilidade then pcall(o.destruirDurabilidade) end
for _, conn in ipairs(o.connections) do
pcall(function() conn:Disconnect() end)
end
o.connections = {}
for plr in pairs(T.bundles) do
pcall(qF, T.bundles[plr])
T.bundles[plr] = nil
end
pcall(function() W:Destroy() end)
pcall(function() S.Cleanup() end)
C.__VETERANO_SINTONIA_WINDOW = nil
C.__VETERANO_SINTONIA_CLEANUP = nil
print("[VETERANO TEAM] Unloaded")
end
print("[VETERANO TEAM] Carregado - Slate Roxo V2 corrigido")
print("[VETERANO TEAM] K = menu | F = fly | N = noclip")

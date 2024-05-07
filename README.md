# Skeet Engine
AKA Acedia Engine or Whistler Engine, a modified cheat engine based off of Netflix Cheat Engine with the toolless method using ClientInputHandler.

## I am completely DEPRECATING Skeet Engine 👋

## what does this mean?

This means I will NO LONGER be updating or working on Acedia Engine, this will be the LAST update to the software on 2024.05.06 4:15 PM EST

## Why is this happening?

Because there are ten times better methods than clunky and old Cheat Engine that other people and myself have been working on.

## Will it still be usable past this date

100% until Roblox patches the CE method COMPLETELY but the repo will still be up as ARCHIVED.

## How to use it as of now 


First use the bootstrapper in my discord server (https://dsc.gg/c00lB055) if you want the newest version or download it from here github.com/J0CKSTRAP/skeet-engine (the ONLY ways to download skeet engine) then extract the rar to where ever is most usable for you
then follow the steps below to get it mostly working in 2024

what you need to do is download bloxstrap 2.5.3 (anything above this removes the Roblox channel reverser) or you could use a tampermonkey script to reverse your channel. 
But this will be for Bloxstrap since it is easier once you have bloxstrap 2.5.3 go to behavior uncheck "Automatically update Bloxstrap" then go to Channel in the behavior tab then type in "LIVE". 
We are not done yet next go into the fastflags tab and type into the box "999". Reference images below!
![image-139](https://github.com/J0CKSTRAP/skeet-engine/assets/81687616/293df4ab-ad5b-40f2-a776-18c39b3496f9)

Then you want to Join this game: https://www.roblox.com/games/16138148062/Sonics-Epic-Xtreme-World then you show see a screen with two buttons "Enable Legacy Mode" and "Leave it disabled" this changes the injectscript name and Skeet Engine will NOT work with Legacy Mode enabled so click "leave it disabled" then teleport to a game (Prison Life is the best for testing in) Once you are in the game of your choose spawn in and hit "Inject" on the window that pops up when CE is launched. then wait until you see something like this:
##
Target Script: ClientInputHandler
{
"user-agent": "Skeet Engine :luascript-HttpGet"
}

once you see this reset your character in the game and if all goes well you should have the ui. Enjoy :3



## Contributing

You can contribute to this archived repo by making your own version since it is 100% open source lua just mod the LUA code below :3

```lua
print("thank you ben_adamim on my discord for the unpatch!")

util = {}
util.allocateMemory = allocateMemory;
util.startThread = executeCode;
util.freeMemory = deAlloc;
openProcess("RobloxPlayerBeta.exe")
openProcess("Windows10Universal.exe")


function renameComponents(c)
    local i
    if c.Component then
      for i=0,c.ComponentCount-1 do
        renameComponents(c.Component[i])
      end
    end
   
    if c.Caption then
      c.Caption='acedia'
    end
  end
   
   
  for i=0,getFormCount()-1 do
      local form = getForm(i)
      for j=0,form.ControlCount-1 do
        renameComponents(form)
      end
   
      form.Caption='Acedia'
  end
   
  registerFormAddNotification(function(f)
    f.registerCreateCallback(function(frm)
      renameComponents(f)
    end)
  end)

local form = MainForm
form.Caption = "Acedia Engine"
util.aobScan = function(aob, code)
    local new_results = {}
    local results = AOBScan(aob, "*X*C*W")
    if not results then
        return new_results
    end
    for i = 1,results.Count do
        local x = getAddress(results[i - 1])
        table.insert(new_results, x)
    end
    return new_results
end

util.intToBytes = function(val)
    if val == nil then
        error'Cannot convert nil value to byte table'
    end
    local t = { val & 0xFF }
    for i = 1,7 do
        table.insert(t, (val >> (8 * i)) & 0xFF)
    end
    return t
end

util.stringToBytes = function(str)
    local result = {}
	for i = 1, #str do
		table.insert(result, string.byte(str, i))
	end
	return result
end

local strexecg = ''

loader = {}
loader.start = function(strexec, pid)
    openProcess("RobloxPlayerBeta.exe")
    openProcess("Windows10Universal.exe")
    local results = util.aobScan("62616E616E6173706C697473????????0C")
    for rn = 1,#results do
        local result = results[rn];
        print(string.format("Result: %08X", result))

        local str = strexec
        local b = {}
        for i = 1, #str + 4 do
            if i <= string.len(str) then
                print('i <= : ' .. i)
                table.insert(b, str:byte(i,i))
            else
                print(i)
                table.insert(b, 0)
            end
        end

        table.insert(b, string.len(str))
        --local b = stringToByteTable(strexec)
        --b[#b + 1] = 0
        writeBytes(result, b)

        closeRemoteHandle(pid)
        strexegc = ""
        -- 8 bytes total
        --[[local b = util.stringToBytes(strexec)
        local bs = 0
        for i = 1,8 do
            table.insert(b, 0)
        end
        for i = 8,1,-1 do
            if b[i] ~= 0 then break end
            bs = bs + 1
        end
        table.insert(b, 8 - bs)
        writeBytes(result, b)]]
    end

    return nil
end

function onOpenProcess(pid)
    print(#strexecg)
    if #strexecg == 0 then
        return
    end
    loader.start(strexecg, pid)
end

loader.start2 = function()

    local players, nameOffset, valid;
    local results = util.aobScan("506C6179657273??????????????????07000000000000000F")
    for rn = 1,#results do
        local result = results[rn];

        if not result then
            return false
        end

        local bres = util.intToBytes(result);
        local aobs = ""
        for i = 1,8 do
            aobs = aobs .. string.format("%02X", bres[i])
        end

        local first = false
        local res = util.aobScan(aobs)
        if res then
            valid = false
            for i = 1,#res do
                result = res[i]
                for j = 1,10 do
                    local ptr = readQword(result - (8 * j))
                    if ptr then
                        ptr = readQword(ptr + 8)
                        if (readString(ptr) == "Players") then

                                print(string.format("Got result: %08X", result))
                                -- go to where the vftable is, 0x18 before classname offset (always)
                                players = (result - (8 * j)) - 0x18
                                -- calculate where we just were
                                nameOffset = result - players
                                value = true
                                break

                        end
                    end
                end
                if valid then break end
            end
        end

        if valid then break end
    end

    print(string.format("Players: %08X", players))
    print(string.format("Name offset: %02X", nameOffset))

    local parentOffset = 0;
    for i = 0x10, 0x120, 8 do
        local ptr = readQword(players + i)
        if ptr ~= 0 and ptr % 4 == 0 then
            if (readQword(ptr + 8) == ptr) then
                parentOffset = i
                break
            end
        end
    end
    print(string.format("Parent offset: %02X", parentOffset))

    local dataModel = readQword(players + parentOffset)
    print(string.format("DataModel: %08X", dataModel))

    local childrenOffset = 0;
    for i = 0x10, 0x200, 8 do
        local ptr = readQword(dataModel + i)
        if ptr then
            local childrenStart = readQword(ptr)
            local childrenEnd = readQword(ptr + 8)
            if childrenStart and childrenEnd then
                if childrenEnd > childrenStart --[[and ((childrenEnd - childrenStart) % 16) == 0]] and childrenEnd - childrenStart > 1 and childrenEnd - childrenStart < 0x1000 then
                    childrenOffset = i
                    break
                end
            end
        end
    end
    print(string.format("Children offset: %02X", childrenOffset))

    local rapi = {}
    rapi.toInstance = function(address)
        return setmetatable({},{
            __index = function(self, name)
                if (name == "self") then
                    return address
                elseif (name == "Name") then
                    local ptr = readQword(self.self + nameOffset);
                    if ptr then
                        local fl = readQword(ptr + 0x18);
                        if fl == 0x1F then
                            ptr = readQword(ptr);
                        end
                        return readString(ptr);
                    else
                        return "???";
                    end
                elseif (name == "className" or name == "ClassName") then
                    local ptr = readQword(self.self + 0x18);
                    ptr = readQword(ptr + 0x8);
                    if ptr then
                        local fl = readQword(ptr + 0x18);
                        if fl == 0x1F then
                            ptr = readQword(ptr);
                        end
                        return readString(ptr);
                    else
                        return "???";
                    end
                elseif (name == "Parent") then
                    return rapi.toInstance(readQword(self.self + parentOffset))
                elseif (name == "getChildren" or name == "GetChildren") then
                    return function(self)
                        local instances = {}
                        local ptr = readQword(self.self + childrenOffset)
                        if ptr then
                            local childrenStart = readQword(ptr + 0)
                            local childrenEnd = readQword(ptr + 8)
                            local at = childrenStart
                            while at < childrenEnd do
                                local child = readQword(at)
                                table.insert(instances, rapi.toInstance(child))
                                at = at + 16
                            end
                        end
                        return instances
                    end
                elseif (name == "findFirstChild" or name == "FindFirstChild") then
                    return function(self, name)
                        for _,v in pairs(self:getChildren()) do
                            if v.Name == name then
                                return v
                            end
                        end
                        return nil
                    end
                elseif (name == "findFirstClass" or name == "FindFirstClass") then
                    return function(self, name)
                        for _,v in pairs(self:getChildren()) do
                            if v.className == name then
                                return v
                            end
                        end
                        return nil
                    end
                    
                elseif (name == "setParent" or name == "SetParent") then
                    return function(self, other)
                        writeQword(self.self + parentOffset, other.self)

                         local newChildren = util.allocateMemory(0x400)
                         writeQword(newChildren + 0, newChildren + 0x40)

                         local ptr = readQword(other.self + childrenOffset)
                         local childrenStart = readQword(ptr + 0)
                         local childrenEnd = readQword(ptr + 8)
                         if not childrenEnd then
                            childrenEnd = 0
                         end
                         if not childrenStart then
                            childrenStart = 0
                         end
                         local b = readBytes(childrenStart, childrenEnd - childrenStart, true)
                         writeBytes(newChildren + 0x40, b)
                         local e = newChildren + 0x40 + (childrenEnd - childrenStart);
                         writeQword(e, self.self)
                         writeQword(e + 8, readQword(self.self + 0x10))
                         e = e + 0x10

                         writeQword(newChildren + 0x8, e)
                         writeQword(newChildren + 0x10, e)
                    end
                else
                    return self:findFirstChild(name)
                end
            end,
            __metatable = "The metatable is locked"
        })
    end

    players = rapi.toInstance(players)
    game = rapi.toInstance(dataModel)

    local localPlayerOffset = 0
    for i = 0x10,0x600,4 do
        local ptr = readQword(players.self + i)
        if readQword(ptr + parentOffset) == players.self then
            localPlayerOffset = i
            break
        end
    end
    print(string.format("Players->LocalPlayer offset: %02X", localPlayerOffset))

    local localPlayer = rapi.toInstance(readQword(players.self + localPlayerOffset));
    print(string.format("Got localplayer: %08X", localPlayer.self))
    print(string.format("Got localplayer: %s", localPlayer.Name))

    local locals = game:findFirstChild("Script Context").StarterScript
    local char = game.Workspace:FindFirstChild(localPlayer.Name)
    --[[for i = 1, #game:findFirstChild("Script Context"):GetChildren() do
        print(game:findFirstChild("Script Context"):GetChildren()[i].Name)
        if game:findFirstChild("Script Context"):GetChildren()[i].Name ~= "CoreScripts/AvatarMood" and game:findFirstChild("Script Context"):GetChildren()[i].Name ~= "CoreScripts/ChatEmoteUsage" then
            locals = game:findFirstChild("Script Context"):GetChildren()[i]
        end
    end]]

    local targetScript = char.ClientInputHandler
    local localscripts = localPlayer.PlayerScripts:GetChildren()
    injectScript = nil
    local results = util.aobScan("616365646961????????????????????06")
    local results2 = util.aobScan("496E6A656374????????????????????06")

    --- regular inj "496E6A656374????????????????????06" 
    for rn = 1, #results do
        local result = results[rn];
       
        local bres = util.intToBytes(result);
       
        local aobs = ""
        for i = 1, 8 do
            aobs = aobs .. string.format("%02X", bres[i])
           
        end
        local first = false
        local res = util.aobScan(aobs)
       
        if res then
            valid = false
            for i = 1,#res do
                result = res[i]
                print(string.format("Result: %08X", result))

                if (readQword(result - nameOffset + 8) == result - nameOffset) then
                    injectScript = result - nameOffset
                    valid = true
                    break
                end
            end

       
        end

        if valid then break end
        
    end
    injectScript = rapi.toInstance(injectScript)
    print(string.format("Inject Script: %08X", injectScript.self))
    -- Copy erm....all the important stuff :------)
    local b = readBytes(injectScript.self + 0x100, 0x150, true)
    writeBytes(targetScript.self + 0x100, b)
    targetScript:SetParent(localPlayer.PlayerScripts)
    print("Target Script: ", targetScript.Name)

local i = getInternet('HttpGet')
local customUserAgent = "Metal/V1.5" -- Replace with your desired user agent
local url = "http://httpbin.org/user-agent"
url = url .. "?User-Agent=" .. customUserAgent -- Append the user agent to the URL
local src = i.getURL(url)
print(src)

end

-- The Main Form
f = createForm()
f.Width = 500
f.Height = 30
f.Position = 'poScreenCenter'
f.Color = '0x232323'
f.BorderStyle = 'bsNone'
f.onMouseDown = DragIt

img_BtnMax = createButton(f)
img_BtnMax.Caption = "Inject"
img_BtnMax.setSize(70,20)
img_BtnMax.setPosition(390,4)
img_BtnMax.onClick = loader.start2

img_BtnClose = createButton(f)
img_BtnClose.setSize(22,22)
img_BtnClose.setPosition(475,4)
img_BtnClose.Stretch = true
img_BtnClose.Cursor = -21
img_BtnClose.Anchors = '[akTop,akRight]'
img_BtnClose.onClick = function()
    print("LoL don't close this!")
end

```

# Goodbye :3


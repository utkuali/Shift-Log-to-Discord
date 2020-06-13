# Shift Log UTK

Logs player shifts to Discord according to their job.

Built for ESX.

## Installation

- Nothing much actually, just pust it in the resources file and start in server.cfg, starting line doesn't matter.

## Configuration

- First set discord webhook, bot's name and bot's avatar in server.lua

- It has police and ambulance job included, if you want to add more jobs just copy the police methods or ambulance. There are comment lines for you to easily find the places you need to edit in order to add more jobs.

## Using with off-duty, on-duty

If you are using an off-duty script, best way to implement this script is to create a serverevent and trigger it in the off-duty script when a player goes off-duty or on-duty.

**IMPORTANT** You need to add the code below to utk_shiftlog/server.lua and trigger the server event from your off-duty script!

Code:

```lua
RegisterServerEvent("utk_sl:dutyChange")
AddEventHandler("utk_sl:dutyChange", function(job, status) -- job is job name | if player gone off duty then you must pass it as false, if player gone on duty you must pass it as true
    local id = source

    if status == false then
        for i = 1, #timers[job], 1 do
            if timers[job][i].id == id then
                local duration = os.time() - timers[job][i].time
                local date = timers[job][i].date
                local timetext, header, color

                if job == "police" then
                    header = "Police Shift"
                    color = 3447003
                elseif job == "ambulance" then
                    header = "EMS Shift"
                    color = 15158332
                end
                if duration > 0 and duration < 60 then
                    timetext = tostring(math.floor(duration)).." seconds"
                elseif duration >= 60 and duration < 3600 then
                    timetext = tostring(math.floor(duration / 60)).." minutes"
                elseif duration >= 3600 then
                    timetext = tostring(math.floor(duration / 3600).." hours, "..tostring(math.floor(math.fmod(duration, 3600)) / 60)).." minutes"
                end
                DiscordLog(header, "Steam Name: **"..timers[job][i].name.."**\nIdentifier: **"..timers[job][i].identifier.."**\n Shift duration: **__"..timetext.."__**\n Start date: **"..date.."**\n End date: **"..os.date("%d/%m/%Y %X").."**", color, job)
                table.remove(timers[job], i)
                return
            end
        end
    elseif status == true then
        local xPlayer = ESX.GetPlayerFromId(id)

        table.insert(timers[job], {id = id, identifier = xPlayer.identifier, name = xPlayer.name, time = os.time(), date = os.date("%d/%m/%Y %X")})
    end
end)
```

Trigger example:

```lua
-- on duty change:
TriggerServerEvent("utk_sl:dutyChange", "police", true) -- the player was off-duty now they are on duty again

TriggerServerEvent("utk_sl:dutyChange", "police", false) -- the player was on-duty now they gone off duty
```

## Contact

[My Discord Channel](https://discord.gg/yqHmvcr) where you can see the details of my scripts and ask for help from me or reach me.

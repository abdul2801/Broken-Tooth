## Description
My buddy Blackbeard with a Broken Blue Tooth keeps preaching about being a sigma, but I caught him vibing to some pookie music. I've captured the packets, help me bully him by identifying the song.

Flag Format: apoorvctf{artist-song}

Example: Never Gonna Give You Up - by Rick Astley
Flag: apoorvctf{rick_astley-never_gonna_give_you_up}

- Author: Afdul
- flag: apoorvctf{billie_eilish-birds_of_a_feather}


## Misc
Note: Solving via rtp streams will not give u the exact audio with the right speed and frequency.Although sadly the song was understandable even with the wrong speed and frequency :/ . mb

## Writeup
Filter all the sbc packets in wireshark.
Filter : `sbc`

Note each packer has 7 fragments. So we need to extract the data from each fragment and combine them to get the flag.
The first 22 bytes will be removed from each packet. The remaining data will be the audio data.


### Start up lua console on wireshark and run the following script to extract the data from each fragment.
    ```
        local filename = "output.bin"
        local file = assert(io.open(filename, "wb"))        

        -- Create a tap listener for SBC packets
        local tap = Listener.new("frame", "sbc")

        function tap.packet(pinfo, tvb)
           

            -- Get the entire raw packet
            local raw_data = tvb:bytes():raw()

            -- Ensure the packet is at least 22 bytes long
            if #raw_data > 22 then
                -- Remove the first 22 bytes
                local trimmed_data = raw_data:sub(23)  -- Lua uses 1-based indexing

                -- Write trimmed data to binary file
                if file then
                    file:write(trimmed_data)
                    file:flush()  -- Ensure data is written immediately
                    print("Packet " .. pinfo.number .. ": Written " .. #trimmed_data .. " bytes to " .. filename)
                else
                    print("Error: File handle is nil")
                end
            else
                print("Packet " .. pinfo.number .. ": Packet too short (<22 bytes), skipped")
            end
        end

        function tap.reset()
            print("Processing reset")
        end

```

The output is is saved in an output.bin file change the file extension to .sbc.

To convert the sbc file to wav file use the following command.
44100 is the sample rate and 2 is the number of channels.
```bash
    ffmpeg -f sbc -i output.sbc -ar 44100 -ac 2 output1.wav
```

The output1.wav file will contain the audio data. Play the audio file to get the flag.
<!-- add the output file from /output/output.wav -->
[Listen to the audio](/output/output.wav)
Blud was listening to Billie Eilish :/ .





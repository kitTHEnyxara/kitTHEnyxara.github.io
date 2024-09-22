layout: page
title: "free on load"
permalink: /loadfree


<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Member Freeing App</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            background-color: #f4f4f4;
            margin: 0;
            padding: 20px;
        }
        #output {
            margin-top: 20px;
            border: 1px solid #ccc;
            padding: 10px;
            background: #fff;
            border-radius: 5px;
            max-height: 300px;
            overflow-y: auto;
        }
        input[type="text"] {
            padding: 10px;
            width: 300px;
            margin-right: 10px;
        }
        input[type="button"] {
            padding: 10px 15px;
        }
    </style>
</head>
<body>

    <h1>
        CommandCombine([{
            Tag: 'loadfree',
            Description: "[Member ID]: Frees the specified member ID after loading.",
            Action: async args => {
                const memberId = parseInt(args.trim(), 10);
                if (isNaN(memberId)) {
                    return; // Invalid input
                }
                await startLoading(memberId);
            }
        }]);

        const totalBlocks = 11; 
        const resolvetime = 666; 
        var originalLabelColor = Player?.LabelColor; 
        var nameColorChangingActive1 = false; 
        var rainbowColors1 = [
            '#FF0000', '#FF7F00', '#FFFF00', '#7FFF00',
            '#00FF00', '#00FF7F', '#00FFFF', '#007FFF',
            '#0000FF', '#7F00FF', '#FF00FF',
        ];
        var currentColorIndex1 = 0; 

        function showProgressBar(progress) {
            const filledBlocks = Math.round((progress / 100) * totalBlocks);
            const emptyBlocks = totalBlocks - filledBlocks;
            return "[" + "#".repeat(filledBlocks) + "--".repeat(emptyBlocks) + "] " + progress + "%";
        }

        function sendProgressUpdate(progress) {
            const outputDiv = document.getElementById('output');
            outputDiv.innerHTML += `<div>Beep: ${CharacterNickname(Player)} is freeing the member in... ${showProgressBar(progress)}</div>`;
        }

        async function freeMemberById(memberId) {
            try {
                const member = ChatRoomCharacter?.find(c => c.MemberNumber === memberId);
                if (!member) {
                    throw new Error(`Member ID ${memberId} not found in the chat room.`);
                }
                member.Appearance = member.Appearance?.filter(x => !x.Asset.Group.Name.match(/Item.*/));
                ChatRoomCharacterUpdate?.(member);
                const outputDiv = document.getElementById('output');
                outputDiv.innerHTML += `<div>Beep: Member ID ${memberId} has been freed!</div>`;
            } catch (error) {
                const outputDiv = document.getElementById('output');
                outputDiv.innerHTML += `<div>Beep: Failed to free member ID ${memberId}: ${error.message}</div>`;
            }
        }

        async function NameColorChanger1() {
            if (!nameColorChangingActive1) return;
            const newColor = rainbowColors1[currentColorIndex1];
            Player?.LabelColor !== undefined && (Player.LabelColor = newColor);
            ServerAccountUpdate?.QueueData({ LabelColor: Player?.LabelColor });
            currentColorIndex1 = (currentColorIndex1 + 1) % rainbowColors1.length;
            await new Promise(resolve => setTimeout(resolve, resolvetime));
        }

        async function startLoading(memberId) {
            let progress = 0;
            originalLabelColor = Player?.LabelColor;
            nameColorChangingActive1 = true;

            while (progress < 100) {
                sendProgressUpdate(progress);
                await new Promise(resolve => setTimeout(resolve, resolvetime));
                progress += 20;
                await NameColorChanger1();
            }

            sendProgressUpdate(100);
            nameColorChangingActive1 = false;
            Player?.LabelColor !== undefined && (Player.LabelColor = originalLabelColor);
            ServerAccountUpdate?.QueueData({ LabelColor: Player?.LabelColor });
            await freeMemberById(memberId);
        }

        // Event listener for the button
        document.getElementById('freeMemberButton').addEventListener('click', () => {
            const memberId = document.getElementById('memberIdInput').value;
            CommandCombine[0].Action(memberId);
        });

</body>
</html>

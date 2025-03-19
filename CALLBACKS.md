# Callbacks

Players can use World Code in custom worlds get functions they've written to run when game events happen. These special functions are called callbacks. The world code can be viewed by pressing F8 by default. Initially the world code will have this comment, which can be removed:

```text
tick onClose onPlayerJoin onPlayerLeave onPlayerJump onRespawnRequest
playerCommand onPlayerChat onPlayerChangeBlock onPlayerDropItem
onPlayerPickedUpItem onPlayerSelectInventorySlot onBlockStand onPlayerCraft
onPlayerAttemptOpenChest onPlayerOpenedChest onPlayerMoveItemOutOfInventory
onPlayerMoveInvenItem onPlayerMoveItemIntoIdxs onPlayerSwapInvenSlots
onPlayerMoveInvenItemWithAmt onPlayerAttemptAltAction onPlayerAltAction
onPlayerClick onClientOptionUpdated onInventoryUpdated onChestUpdated
onWorldChangeBlock onCreateBloxdMeshEntity onEntityCollision
onPlayerAttemptSpawnMob onWorldAttemptSpawnMob onPlayerSpawnMob
onWorldSpawnMob onMobDespawned onPlayerAttack onPlayerDamagingOtherPlayer
onPlayerDamagingMob onPlayerKilledOtherPlayer onMobKilledPlayer
onPlayerKilledMob onPlayerPotionEffect onPlayerDamagingMeshEntity
onPlayerBreakMeshEntity onPlayerUsedThrowable onPlayerThrowableHitTerrain
onTouchscreenActionButton onTaskClaimed onChunkLoaded onPlayerRequestChunk
onItemDropCreated onPlayerStartChargingItem onPlayerFinishChargingItem
doPeriodicSave

To use a callback, just assign a function to it in the world code!
tick = () => {}			 or			 function tick() {}
```

Right now callbacks written by players will have their return values ignored (treated as undefined). The LoadedChunk argument which is normally passed to onChunkLoaded will also always be null.

```ts
/**
 * Called every tick, 20 times per second
 * @param dt - The time since the last tick in milliseconds
 */
tick: (dt) => {}

/**
 * Called when the lobby is shutting down
 * @param serverIsShuttingDown - Whether the server is shutting down
 */
onClose: (serverIsShuttingDown) => {}

/**
 * Called when a player joins the lobby
 * @param playerId - The id of the player that joined
 */
onPlayerJoin: (playerId) => {}

/**
 * Called when a player leaves the lobby
 * @param playerId - The id of the player that left
 * @param serverIsShuttingDown - Whether the server is shutting down
 */
onPlayerLeave: (playerId, serverIsShuttingDown) => {}

/**
 * Called when a player jumps
 * @param playerId - The id of the player that jumped
 */
onPlayerJump: (playerId) => {}

/**
 * Called when a player requests to respawn.
 * Optionally return the respawn location. Defaults to [0, 0, 0].
 * Return true to handle yourself (good for async,
 * but be careful that the player isn't at the place they died,
 * as they could pick up their old items or hit the player they were fighting).
 * @param playerId - The id of the player that requested to respawn
 */
onRespawnRequest: (playerId) => {}

/**
 * Called when a player sends a command
 * @param playerId - The id of the player that sent the command
 * @param command - The command that the player sent
 */
playerCommand: (playerId, command) => {
    return false
}

/**
 * Called when a player sends a chat message
 * Return false to prevent the broadcast of the message.
 * Return CustomTextStyling to add a prefix to message.
 * Return for most flexibility: an object where keys are playerIds -
 * the value for a playerId being false means that player won't receive the message.
 * Otherwise playerId values should be an object with (optional) keys
 * prefixContent and chatContent to modify the prefix and the chat.
 * @param playerId - The id of the player that sent the message
 * @param chatMessage - The message that the player sent
 * @param channelName - The name of the channel that the message was sent in
 */
onPlayerChat: (playerId: PlayerId, chatMessage: string, channelName?: string) => {
    return true
}

/**
 * Called when a player changes a block
 * Return "preventChange" to prevent the change.
 * If player places block, fromBlock will be Air (and toBlock the block).
 * If a player breaks a block, toBlock will be Air.
 * Return "preventDrop" to prevent a block item from dropping.
 * Return an array to set the dropped item position.
 */
onPlayerChangeBlock: (
    playerId: PlayerId, // The id of the player that changed the block
    x: number, // The x coordinate of the block that was changed
    y: number, // The y coordinate of the block that was changed
    z: number, // The z coordinate of the block that was changed
    fromBlock: BlockName, // The old block that was replaced
    toBlock: BlockName, // The new block that was placed
    droppedItem: BlockName | null, // The item that was dropped
    fromBlockInfo: MultiBlockInfo, // The info of the old block that was replaced
    toBlockInfo: MultiBlockInfo, // The info of the new block that was placed
) => {}

/**
 * Called when a player drops an item
 * Return "preventDrop" to prevent the player from dropping the item at all.
 * Return "allowButNoDroppedItemCreated" to allow discarding items without dropping them.
 */
onPlayerDropItem: (
    playerId: PlayerId, // The id of the player that dropped the item
    x: number, // The x coordinate of the item that was dropped
    y: number, // The y coordinate of the item that was dropped
    z: number, // The z coordinate of the item that was dropped
    itemName: ItemName, // The name of the item that was dropped
    itemAmount: number, // The amount of the item that was dropped
    fromIdx: number, // The index of the item that was dropped from the player's inventory
) => {}

/**
 * Called when a player picks up an item
 * @param playerId - The id of the player that picked up the item
 * @param itemName - The name of the item that was picked up
 * @param itemAmount - The amount of the item that was picked up
 */
onPlayerPickedUpItem: (playerId: PlayerId, itemName: string, itemAmount: number) => {}

/**
 * Called when a player selects a different inventory slot.
 * This will be called eventually when you have already set the slot using
 * api.setSelectedInventorySlotI so be careful not to cause an infinite loop doing this.
 * @param playerId - The id of the player that selected the inventory slot
 * @param slotIndex - The index of the inventory slot that was selected
 */
onPlayerSelectInventorySlot: (playerId: PlayerId, slotIndex: number) => {}

/**
 * Called when a player stands on a block
 */
onBlockStand: (
    playerId: PlayerId, // The id of the player that stood on the block
    x: number, // The x coordinate of the block that was stood on
    y: number, // The y coordinate of the block that was stood on
    z: number, // The z coordinate of the block that was stood on
    blockName: BlockName, // The name of the block that was stood on
) => {}

/**
 * Called when a player crafts an item
 * Return "preventCraft" to prevent a craft from happening
 * @param playerId - The id of the player that crafted the item
 * @param itemName - The name of the item that was crafted
 * @param craftingIdx - The index of the used recipe in the item's recipe list
 */
onPlayerCraft: (playerId: PlayerId, itemName: string, craftingIdx: number) => {}

/**
 * Called when a player attempts to open a chest
 * Return "preventOpen" to prevent the player from opening the chest
 */
onPlayerAttemptOpenChest: (
    playerId: PlayerId, // The id of the player that is attempting to open the chest
    x: number, // The x coordinate of the chest that the player is attempting to open
    y: number, // The y coordinate of the chest that the player is attempting to open
    z: number, // The z coordinate of the chest that the player is attempting to open
    isMoonstoneChest: boolean, // Whether the chest is a moonstone chest
) => {}

/**
 * Called when a player opens a chest
 */
onPlayerOpenedChest: (
    playerId: PlayerId, // The id of the player that opened the chest
    x: number, // The x coordinate of the chest that was opened
    y: number, // The y coordinate of the chest that was opened
    z: number, // The z coordinate of the chest that was opened
    isMoonstoneChest: boolean, // Whether the chest is a moonstone chest
) => {}

/**
 * Called when a player moves an item out of their inventory
 * Return "preventChange" to prevent the movement
 */
onPlayerMoveItemOutOfInventory: (
    playerId: PlayerId, // The id of the player moving the item
    itemName: string, // The name of the item being moved
    itemAmount: number, // The amount of the item being moved
    fromIdx: number, // The index which the item is being moved from
    movementType: string, // The type of movement that occurred
) => {}

/**
 * Called for all types of inventory item movement.
 * Certain methods of moving item can result in splitting a stack
 * into multiple slots. (e.g. shift-click).
 * toStartIdx and toEndIdx provide the min and max idxs moved into.
 * Return "preventChange" to prevent item movement.
 */
onPlayerMoveInvenItem: (
    playerId: PlayerId, // The id of the player moving the item
    fromIdx: number, // The index that the item is being moved from
    toStartIdx: number, // The start index that the item is being moved into
    toEndIdx: number, // The end index that the item is being moved into
    amt: number, // The amount of the item being moved
) => {}

/**
 * Called when a player moves an item into an index within a range of inventory slots
 * Return "preventChange" to prevent the movement
 */
onPlayerMoveItemIntoIdxs: (
    playerId: PlayerId, // The id of the player moving the item
    start: number, // The start index of the range
    end: number, // The end index of the range
    moveIdx: number, // The index of the item being moved
    itemAmount: number, // The amount of the item being moved
) => {}

/**
 * Return "preventChange" to prevent the swap
 * @param playerId - The id of the player swapping the inventory slots
 * @param i - The index of the first slot
 * @param j - The index of the second slot
 */
onPlayerSwapInvenSlots: (playerId: PlayerId, i: number, j: number) => {}

/**
 * Return "preventChange" to prevent the movement
 * @param playerId - The id of the player moving the item
 * @param i - The index of the first slot
 * @param j - The index of the second slot
 * @param amt - The amount of the item being moved
 */
onPlayerMoveInvenItemWithAmt: (playerId: PlayerId, i: number, j: number, amt: number) => {}

/**
 * Called when player alt actions (right click on pc).
 * The co-ordinates will be undefined if there is no targeted block (and block will be "Air")
 */
onPlayerAttemptAltAction: (
    playerId: PlayerId, // The id of the player attempting the alt action
    x: number, // The x coordinate of the targeted block
    y: number, // The y coordinate of the targeted block
    z: number, // The z coordinate of the targeted block
    block: BlockName, // The name of the targeted block
    targetEId: EntityId | null, // The id of the targeted entity
) => {}

/**
 * Called when player completes an alt action (right click on pc).
 * The co-ordinates will be undefined if there is no targeted block (and block will be "Air")
 */
onPlayerAltAction: (
    playerId: PlayerId, // The id of the player completing the alt action
    x: number, // The x coordinate of the targeted block
    y: number, // The y coordinate of the targeted block
    z: number, // The z coordinate of the targeted block
    block: BlockName, // The name of the targeted block
    targetEId: EntityId | null, // The id of the targeted entity
) => {}

/**
 * Called when a player clicks
 * Don't have important functionality depending on wasAltClick,
 * as it'll always be false for touchscreen players.
 */
onPlayerClick: (
    playerId: PlayerId, // The id of the player clicking
    wasAltClick: boolean, // Whether the click was an alt click (e.g. right click)
) => {}

/**
 * Called when a client option is updated
 * @param playerId - The id of the player whose option was updated
 * @param option - The option that was updated
 * @param value - The new value of the option
 */
onClientOptionUpdated: (playerId: PlayerId, option: ClientOption, value: any) => {}

/**
 * Called when a player's inventory is updated
 * @param playerId - The id of the player whose inventory was updated
 */
onInventoryUpdated: (playerId: PlayerId) => {}

/**
 * Called when a chest is updated by a player
 * x, y, z, will be null if isMoonstoneChest is true
 */
onChestUpdated: (
    initiatorEId: PlayerId, // The id of the player who updated the chest
    isMoonstoneChest: boolean, // Whether the chest is a moonstone chest
    x: number | null, // The x coordinate of the chest
    y: number | null, // The y coordinate of the chest
    z: number | null, // The z coordinate of the chest
) => {}

/**
 * Called when a block is changed in the world
 * initiatorDbId is null if updated by game code e.g. when a sapling grows
 * Return "preventChange" to prevent change
 * Return "preventDrop" to prevent a block item from dropping
 */
onWorldChangeBlock: (
    x: number, // The x coordinate of the block
    y: number, // The y coordinate of the block
    z: number, // The z coordinate of the block
    fromBlock: BlockName, // The old block that was replaced
    toBlock: BlockName, // The new block that was placed
    initiatorDbId: string | null, // The id of the player who updated the block
) => {}

/**
 * Called when a mesh entity is created
 * @param eId - The id of the mesh entity
 * @param type - The type of mesh entity
 */
onCreateBloxdMeshEntity: (eId: EntityId, type: string) => {}

/**
 * Called when a entity collides with another entity
 * @param eId - The id of the entity
 * @param otherEId - The id of the other entity
 */
onEntityCollision: (eId: EntityId, otherEId: EntityId) => {}

/**
 * Called when a player attempts to spawn a mob, e.g. using a spawn orb.
 * Return "preventSpawn" to prevent the mob from spawning.
 */
onPlayerAttemptSpawnMob: (
    playerId: PlayerId, // The id of the player
    mobType: MobType, // The type of mob
    x: number, // The potential x coordinate of the mob
    y: number, // The potential y coordinate of the mob
    z: number, // The potential z coordinate of the mob
) => {}

/**
 * Called when the world attempts to spawn a mob.
 * Return "preventSpawn" to prevent the mob from spawning.
 * @param mobType - The type of mob
 * @param x - The potential x coordinate of the mob
 * @param y - The potential y coordinate of the mob
 * @param z - The potential z coordinate of the mob
 */
onWorldAttemptSpawnMob: (mobType: MobType, x: number, y: number, z: number) => {}

/**
 * Called when a mob is spawned by a player
 */
onPlayerSpawnMob: (
    playerId: PlayerId, // The id of the player who spawned the mob
    mobId: MobId, // The id of the mob
    mobType: MobType, // The type of mob
    x: number, // The x coordinate of the mob
    y: number, // The y coordinate of the mob
    z: number, // The z coordinate of the mob
    mobHerdId: MobHerdId, // The herd id of the mob
    playSoundOnSpawn: boolean, // Whether to play a sound on spawn
) => {}

/**
 * Called when a mob is spawned by the world
 */
onWorldSpawnMob: (
    mobId: MobId, // The id of the mob
    mobType: MobType, // The type of mob
    x: number, // The x coordinate of the mob
    y: number, // The y coordinate of the mob
    z: number, // The z coordinate of the mob
    mobHerdId: MobHerdId, // The herd id of the mob
    playSoundOnSpawn: boolean, // Whether to play a sound on spawn
) => {}

/**
 * Called when a mob is despawned
 * @param mobId - The id of the mob despawned
 */
onMobDespawned: (mobId: MobId) => {}

/**
 * Called when a player attacks another player
 * @param playerId - The id of the player attacking
 */
onPlayerAttack: (playerId) => {}

/**
 * Called when a player is damaging another player
 * Return "preventDamage" to prevent damage
 * Return number to change damage dealt to that amount
 * Sometimes the damager will have left the game (e.g. spikes placer);
 * in this case, attackingPlayer will be the damagedPlayer,
 * but we pass damagerDbId for use cases where it's important.
 */
onPlayerDamagingOtherPlayer: (
    attackingPlayer: PlayerId, // The id of the player attacking
    damagedPlayer: PlayerId, // The id of the player being damaged
    damageDealt: number, // The amount of damage dealt
    withItem: string, // The item used to attack
    bodyPartHit: PlayerBodyPart, // The body part hit
    damagerDbId: PlayerDbId, // The database id of the player attacking
) => {}

/**
 * Called when a player is damaging a mob
 */
onPlayerDamagingMob: (
    playerId: PlayerId, // The id of the player damaging the mob
    mobId: MobId, // The id of the mob being damaged
    damageDealt: number, // The amount of damage dealt
    withItem: string, // The item used to attack
) => {}

/**
 * Called when a player kills another player
 * Return "keepInventory" to not drop the player's inventory
 * @param attackingPlayer - The id of the player attacking
 * @param killedPlayer - The id of the player killed
 * @param damageDealt - The amount of damage dealt
 * @param withItem - The item used to attack
 */
onPlayerKilledOtherPlayer: (attackingPlayer, killedPlayer, damageDealt, withItem) => {}

/**
 * Called when a mob kills a player
 * Return "keepInventory" to not drop the player's inventory
 * @param attackingMob - The id of the mob attacking
 * @param killedPlayer - The id of the player killed
 * @param damageDealt - The amount of damage dealt
 * @param withItem - The item used to attack
 */
onMobKilledPlayer: (attackingMob, killedPlayer, damageDealt, withItem) => {}

/**
 * Called when a mob kills a player
 * Return "preventDrop" to prevent the mob from dropping items
 */
onPlayerKilledMob: (
    playerId: PlayerId, // The id of the player killed
    mobId: MobId, // The id of the mob that killed the player
    damageDealt: number, // The amount of damage dealt
    withItem: string, // The item used to attack
) => {}

/**
 * Called when a player is affected by a new potion effect
 * @param initiatorId - The id of the player who initiated the potion effect
 * @param targetId - The id of the player who has started being affected
 * @param effectName - The name of the potion effect
 */
onPlayerPotionEffect: (initiatorId, targetId, effectName) => {}

/**
 * Called when a player is damaging a mesh entity
 */
onPlayerDamagingMeshEntity: (
    playerId: PlayerId, // The id of the player damaging the mesh entity
    damagedId: EntityId, // The id of the mesh entity being damaged
    damageDealt: number, // The amount of damage dealt
    withItem: string, // The item used to attack
) => {}

/**
 * Called when a player breaks a mesh entity
 * @param playerId - The id of the player breaking the mesh entity
 * @param entityId - The id of the mesh entity being broken
 */
onPlayerBreakMeshEntity: (playerId: PlayerId, entityId: EntityId) => {}

/**
 * Called when a player uses a throwable item
 */
onPlayerUsedThrowable: (
    playerId: PlayerId, // The id of the player using the throwable item
    throwableName: ThrowableItem, // The name of the throwable item
    thrownEntityId: EntityId, // The id of the projectile created by the player
) => {}

/**
 * Called when a player's thrown projectile hits the terrain
 */
onPlayerThrowableHitTerrain: (
    playerId: PlayerId, // The id of the player that threw the throwable item
    throwableName: ThrowableItem, // The name of the throwable item
    thrownEntityId: EntityId, // The id of the entity which hit the terrain
) => {}

/**
 * Set client option `touchscreenActionButton` to take effect
 * Called when a player presses the touchscreen action button
 * Called for both touchDown and touchUp
 * @param playerId - The id of the player pressing the touchscreen action button
 * @param touchDown - Whether the touchscreen action button was pressed or released
 */
onTouchscreenActionButton: (playerId: PlayerId, touchDown: boolean) => {}

/**
 * Called when a player claims a task
 * @param playerId - The id of the player claiming the task
 * @param taskId - The id of the task being claimed
 * @param isPromoTask - Whether the task is a promo task
 * @param claimedRewards - The rewards claimed by the player
 */
onTaskClaimed: (playerId, taskId, isPromoTask, claimedRewards) => {}

/**
 * Called when a chunk is first loaded
 * @param chunkId - The id of the chunk being loaded
 * @param chunk - The chunk being loaded, which can be modified by this callback
 * @param wasPersistedChunk - Whether the chunk was persisted
 */
onChunkLoaded: (chunkId: string, chunk: LoadedChunk, wasPersistedChunk: boolean) => {}

/**
 * Called when a player requests a chunk
 */
onPlayerRequestChunk: (
    playerId: PlayerId, // The id of the player requesting the chunk
    chunkX: number, // The x coordinate of the chunk being requested
    chunkY: number, // The y coordinate of the chunk being requested
    chunkZ: number, // The z coordinate of the chunk being requested
    chunkId: string, // The id of the chunk being requested
) => {}

/**
 * Called when an item drop is created
 */
onItemDropCreated: (
    itemEId: EntityId, // The id of the item drop
    itemName: string, // The name of the item dropped
    itemAmount: number, // The amount dropped
    x: number, // The x coordinate of the item drop
    y: number, // The y coordinate of the item drop
    z: number, // The z coordinate of the item drop
) => {}

/**
 * Called when a player starts charging an item
 * @param playerId - The id of the player charging the item
 * @param itemName - The name of the item being charged
 */
onPlayerStartChargingItem: (playerId: PlayerId, itemName: string) => {}

/**
 * Called when a player finishes charging an item
 */
onPlayerFinishChargingItem: (
    playerId: PlayerId, // The id of the player charging the item
    used: boolean, // Whether the item was used
    itemName: string, // The name of the charged item
    duration: number, // The duration of the charge
) => {}

/**
 * Called every so often.
 * You should save custom db values/s3 objects here.
 * Persisted items ARE saved on graceful shutdown (e.g. uncaught error, update, etc),
 * but this helps prevent large data-loss on non-graceful shutdowns.
 */
doPeriodicSave: () => {}
```

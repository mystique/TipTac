# TipTac fix for Shadowlands 9.0.2

fix tooltip blink when mouse over time buff/debuff with timer:

```lua
function tt:HookTips()
	-- https://www.curseforge.com/wow/addons/tip-tac/issues/212

	-- add following code before last line
	GameTooltip_SetDefaultAnchor(GameTooltip, UIParent)
	hooksecurefunc(GameTooltip, "SetUnitAura", function(self, ...)
		if self:GetAnchorType() ~= 'ANCHOR_NONE' then
			self:SetAnchorType('ANCHOR_NONE');
		end
	end);
	-- end add
 
	-- Clear this function as it's not needed anymore
	self.HookTips = nil;
end
```

auto adjust tooltip position when over the edge:
```lua
function tt:AnchorFrameToMouse(frame)
	local x, y = GetCursorPosition();
	local effScale = frame:GetEffectiveScale();
	local frameX, frameY = (x / effScale + cfg.mouseOffsetX), (y / effScale + cfg.mouseOffsetY);
	local frameW, frameH = frame:GetWidth(), frame:GetHeight();
	local screenW, screenH = GetScreenWidth(), GetScreenHeight();

	local adjustAnchorPoint = gtt_anchorPoint;

	if (frameX+frameW > screenW) then
		adjustAnchorPoint = "TOPRIGHT";

		if (frameH > frameY) then
			adjustAnchorPoint = "BOTTOMRIGHT";
		end
		frameX = frameX - cfg.mouseOffsetX;
		frameY = frameY - cfg.mouseOffsetY;
	elseif (frameH > frameY) then
		adjustAnchorPoint = "BOTTOMLEFT";
	end

	frame:ClearAllPoints();
	frame:SetPoint(adjustAnchorPoint,UIParent,"BOTTOMLEFT",frameX,frameY);
end
```

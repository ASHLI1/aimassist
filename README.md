package com.example.aimassist;

import net.minecraft.client.Minecraft;
import net.minecraft.client.entity.EntityPlayerSP;
import net.minecraft.entity.Entity;
import net.minecraft.entity.EntityLivingBase;
import net.minecraft.src.ModLoader;
import org.lwjgl.input.Keyboard;

import java.util.List;

public class AimAssistMod {
    private static final Minecraft mc = Minecraft.getMinecraft();
    private static final int AIM_KEY = Keyboard.KEY_R; // Change to your preferred key

    public void onTickInGame(float partialTicks) {
        if (Keyboard.isKeyDown(AIM_KEY)) {
            aimAtNearestEntity();
        }
    }

    private void aimAtNearestEntity() {
        EntityPlayerSP player = mc.thePlayer;
        if (player == null || mc.theWorld == null) return;

        List<Entity> entities = mc.theWorld.loadedEntityList;
        EntityLivingBase nearest = null;
        double minDistance = Double.MAX_VALUE;

        for (Entity entity : entities) {
            if (entity instanceof EntityLivingBase && entity != player) {
                double dist = player.getDistanceToEntity(entity);
                if (dist < minDistance) {
                    minDistance = dist;
                    nearest = (EntityLivingBase) entity;
                }
            }
        }

        if (nearest != null) {
            double dx = nearest.posX - player.posX;
            double dy = (nearest.posY + nearest.getEyeHeight()) - (player.posY + player.getEyeHeight());
            double dz = nearest.posZ - player.posZ;
            double dist = Math.sqrt(dx * dx + dz * dz);
            float yaw = (float) (Math.atan2(dz, dx) * 180.0D / Math.PI) - 90.0F;
            float pitch = (float) (-(Math.atan2(dy, dist) * 180.0D / Math.PI));
            player.rotationYaw = yaw;
            player.rotationPitch = pitch;
        }
    }

    public static void load() {
        ModLoader.setInGameHook(new AimAssistMod(), true, false);
    }
}# aimassist
It is an aimassist mod which makes your aim clear 

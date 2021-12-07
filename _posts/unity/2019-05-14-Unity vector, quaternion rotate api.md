---
layout: post
title: Unity vector, quaternion rotate api
categories: [C#, Unity]
---

##  Vector Rotate
```c#
Vector3 direction = some value;
direction = Quaternion.Euler(0.0f, 0.0f, deltaAngle) * direction;
```

```c#
Vector2 toTarget = (target.position - transform.position).normalized;
float step = Time.deltaTime * rotateAngleSpeed * Mathf.Deg2Rad; 
rotateVector = Vector3.RotateTowards(rotateVector, toTarget, step, 0.0f);
```

***

##  Quaterinon Rotate

```c#
Quaternion nextQuaternion = Quaternion.RotateTowards(sourceQuaternion, destQuaternion, Time.deltaTime * angleSpeed);
```

```c#
Vector2 toTargetDir = (Vector2)targetPosition - (Vector2)transform.position;
var quaternionToTarget = Quaternion.FromToRotation(transform.right, toTargetDir.normalize) * transform.rotation;
transform.rotation = Quaternion.RotateTowards(transform.rotation, quaternionToTarget, Time.deltaTime * rotateSpeed);
```

***

## GetSignAngle
``` c#
public static float GetSignAngle(Vector3 v1, Vector3 v2)
{
  int sign = Vector3.Cross(v1, v2).z < 0 ? -1 : 1;
  return sign * Vector3.Angle(v1, v2);
}
```

## GetAngleTwoVector
``` c#
public static float GetAngleTwoVector(Vector2 v1, Vector2 v2)
{
    return Mathf.Acos(Vector2.Dot(v1, v2)) * (180 / Mathf.PI);
}

public static float GetNegativeAngle(Vector3 v1, Vector3 v2)
{
    int sign = Vector3.Cross(v1, v2).z < 0 ? -1 : 1;
    return sign * Vector3.Angle(v1, v2);
}
```

## DirectionToAngle, AngleToDirection
``` c#
public static float DirectionToAngle(float x, float y)
{
    return DirectionToAngle(new Vector2(x, y));
}

public static float DirectionToAngle(Vector2 direction)
{
    return Mathf.Atan2(direction.y, direction.x) * Mathf.Rad2Deg;
}

public static float DirectionToAngle(Vector3 direction)
{
    return Mathf.Atan2(direction.y, direction.x) * Mathf.Rad2Deg;
}

public static Vector2 AngleToDirection(float degree)
{
    var radius = degree * Mathf.Deg2Rad;
    return new Vector2(Mathf.Cos(radius), Mathf.Sin(radius));
}
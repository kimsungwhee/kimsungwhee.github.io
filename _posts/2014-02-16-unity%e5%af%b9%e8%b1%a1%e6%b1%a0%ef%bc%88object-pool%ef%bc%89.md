---
title: '[Unity]对象池（Object Pool）'
author: admin
layout: post
permalink: /unity%e5%af%b9%e8%b1%a1%e6%b1%a0%ef%bc%88object-pool%ef%bc%89/
categories:
  - Unity
tags:
  - 对象池
---
<pre class="lang:default decode:true">using UnityEngine;
using System.Collections;
using System.Collections.Generic;

public sealed class ObjectPool : MonoBehaviour
{
	static ObjectPool _instance;

	Dictionary&lt;Component, List&lt;Component&gt;&gt; objectLookup = new Dictionary&lt;Component, List&lt;Component&gt;&gt;();
	Dictionary&lt;Component, Component&gt; prefabLookup = new Dictionary&lt;Component, Component&gt;();

	public static void Clear()
	{
		instance.objectLookup.Clear();
		instance.prefabLookup.Clear();
	}

	public static void CreatePool&lt;T&gt;(T prefab) where T : Component
	{
		if (!instance.objectLookup.ContainsKey(prefab))
			instance.objectLookup.Add(prefab, new List&lt;Component&gt;());
	}

	public static T Spawn&lt;T&gt;(T prefab, Vector3 position, Quaternion rotation) where T : Component
	{
		if (instance.objectLookup.ContainsKey(prefab))
		{
			T obj = null;
			var list = instance.objectLookup[prefab];
			if (list.Count &gt; 0)
			{
				while (obj == null && list.Count &gt; 0)
				{
					obj = list[0] as T;
					list.RemoveAt(0);
				}
				if (obj != null)
				{
					obj.transform.parent = null;
					obj.transform.localPosition = position;
					obj.transform.localRotation = rotation;
					obj.gameObject.SetActive(true);
					instance.prefabLookup.Add(obj, prefab);
					return (T)obj;
				}
			}
			obj = (T)Object.Instantiate(prefab, position, rotation);
			instance.prefabLookup.Add(obj, prefab);
			return (T)obj;
		}
		else
			return (T)Object.Instantiate(prefab, position, rotation);
	}
	public static T Spawn&lt;T&gt;(T prefab, Vector3 position) where T : Component
	{
		return Spawn(prefab, position, Quaternion.identity);
	}
	public static T Spawn&lt;T&gt;(T prefab) where T : Component
	{
		return Spawn(prefab, Vector3.zero, Quaternion.identity);
	}

	public static void Recycle&lt;T&gt;(T obj) where T : Component
	{
		if (instance.prefabLookup.ContainsKey(obj))
		{
			instance.objectLookup[instance.prefabLookup[obj]].Add(obj);
			instance.prefabLookup.Remove(obj);
			obj.transform.parent = instance.transform;
			obj.gameObject.SetActive(false);

		}
		else
			Object.Destroy(obj.gameObject);
	}

	public static int Count&lt;T&gt;(T prefab) where T : Component
	{
		if (instance.objectLookup.ContainsKey(prefab))
			return instance.objectLookup[prefab].Count;
		else
			return 0;
	}

	public static ObjectPool instance
	{
		get
		{
			if (_instance != null)
				return _instance;
			var obj = new GameObject("_ObjectPool");
			obj.transform.localPosition = Vector3.zero;
			_instance = obj.AddComponent&lt;ObjectPool&gt;();
			return _instance;
		}
	}
}

public static class ObjectPoolExtensions
{
	public static void CreatePool&lt;T&gt;(this T prefab) where T : Component
	{
		ObjectPool.CreatePool(prefab);
	}

	public static T Spawn&lt;T&gt;(this T prefab, Vector3 position, Quaternion rotation) where T : Component
	{
		return ObjectPool.Spawn(prefab, position, rotation);
	}
	public static T Spawn&lt;T&gt;(this T prefab, Vector3 position) where T : Component
	{
		return ObjectPool.Spawn(prefab, position, Quaternion.identity);
	}
	public static T Spawn&lt;T&gt;(this T prefab) where T : Component
	{
		return ObjectPool.Spawn(prefab, Vector3.zero, Quaternion.identity);
	}

	public static void Recycle&lt;T&gt;(this T obj) where T : Component
	{
		ObjectPool.Recycle(obj);
	}

	public static int Count&lt;T&gt;(T prefab) where T : Component
	{
		return ObjectPool.Count(prefab);
	}
}</pre>

使用方法：

<pre class="lang:default decode:true ">using UnityEngine;
using System.Collections;

public class Turret : MonoBehaviour
{
	public Bullet bulletPrefab;

	void Start()
	{
		bulletPrefab.CreatePool();//创建对象池
	}

	void Update()
	{

			if (Input.GetMouseButtonDown(0))
				bulletPrefab.Spawn(目标.position, 目标.rotation);
		}
	}
}</pre>

&nbsp;
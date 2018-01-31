---
id: 60
title: 二叉查找树(Binary Search Tree)
date: 2009-05-13T15:03:57+08:00
author: Benny Chen
layout: post
guid: http://www.bennychen.cn/?p=60
permalink: '/2009/05/%e4%ba%8c%e5%8f%89%e6%9f%a5%e6%89%be%e6%a0%91binary-search-tree/'
syntaxhighlighter_encoded:
  - "1"
sfw_pwd:
  - lg3isNqd5v5D
categories:
  - Talking in Code
tags:
  - binary search
  - tree
---
今天下午闲着无聊，复习了一下“二叉查找树”，并用代码温习了一下。
  
对于二叉查找树，一般支持的操作有：查找关键字，最大值，最小值，前驱和后继等等的查询，对于高度为h的树，它们都可以在O(h)时间内完成。

BinaryTree.h

<pre class="brush: cpp; collapse: true; light: false; title: ; toolbar: true; notranslate" title="">#ifndef __BINARY_TREE_H__
#define __BINARY_TREE_H__

template &lt; typename Key, typename Data &gt; class BinaryTree;

/*! 树的节点 */
template &lt; typename Key, typename Data &gt;
class TreeNode
{
public:
	//! 默认构造函数
	TreeNode()
		: m_pParent( NULL ),
		  m_pLChild( NULL ),
		  m_pRChild( NULL )
	{
	}

	//! 构造函数
	TreeNode( Key k, const Data & d )
		: m_pParent( NULL ),
		  m_pLChild( NULL ),
		  m_pRChild( NULL ),
		  m_key( k ),
		  m_data( d )
	{
	}

	//! 拷贝构造函数
	TreeNode( const TreeNode&lt; Key, Data &gt; & node )
		: m_pParent( NULL ),
		  m_pLChild( NULL ),
		  m_pRChild( NULL ),
		  m_key( node.m_key ),
		  m_data( node.m_data )
	{
	}

	//! 赋值函数
	TreeNode&lt; Key, Data &gt; & operator=( const TreeNode&lt; Key, Data &gt; & rhs )
	{
		m_key = rhs.m_key;
		m_data = rhs.m_data;
		return *this;
	}

	//! 获取键值
	Key GetKey() const { return m_key; }
	//! 获取数据
	Data GetData() const { return m_data; }
	//! 设置数据
	void SetData( const Data & d ) { m_data = d; }

private:
	Key			m_key;					//!&lt; 关键字
	Data		m_data;					//!&lt; 数据
	TreeNode	*m_pParent;				//!&lt; 父节点
	TreeNode	*m_pLChild;				//!&lt; 左子节点
	TreeNode	*m_pRChild;				//!&lt; 右子节点

	friend class BinaryTree&lt; Key, Data &gt;;
};

/*! 二叉查找树 */
template &lt; typename Key, typename Data &gt;
class BinaryTree
{
public:
	BinaryTree()
		:m_pRoot( NULL )
	{
	}

	~BinaryTree()
	{
		while ( m_pRoot != NULL )
		{
			DeleteNode( m_pRoot );
		}
	}

	typedef TreeNode&lt; Key, Data &gt; _TreeNode;

	//! 查找
	_TreeNode * Search( const Key & destK ) const;

	//! 最小值
	_TreeNode * Minimum( _TreeNode * pNode ) const;

	//! 最大值
	_TreeNode * Maximum( _TreeNode * pNode ) const;

	//! 后继
	_TreeNode * Successor( _TreeNode * pNode ) const;

	//! 前任
	_TreeNode * Predecessor( _TreeNode * pNode ) const;

	//! 添加节点
	void AddNode( const _TreeNode * pNode );

	//! 删除节点
	void DeleteNode( _TreeNode * pNode );

	//! 获取根节点
	_TreeNode * GetRootNode() const { return m_pRoot; }

private:
	_TreeNode	*m_pRoot;	//! 根节点
};

template &lt; typename Key, typename Data &gt;
void BinaryTree&lt; Key, Data &gt;::AddNode( const _TreeNode * pNode )
{
	assert( NULL != pNode &&
		"pNode cannot be NULL" );

	_TreeNode *newNode = new _TreeNode( *pNode );
	_TreeNode *n1 = m_pRoot;
	_TreeNode *n2 = NULL;
	while ( NULL != n1 )
	{
		n2 = n1;
		if ( newNode-&gt;m_key &lt; n2-&gt;m_key )
		{
			n1 = n2-&gt;m_pLChild;
		}
		else
		{
			n1 = n2-&gt;m_pRChild;
		}
	}
	if ( NULL == m_pRoot )
	{
		m_pRoot = newNode;
	}
	else
	{
		if ( newNode-&gt;m_key &lt; n2-&gt;m_key )
		{
			n2-&gt;m_pLChild = newNode;
		}
		else
		{
			n2-&gt;m_pRChild = newNode;
		}
		newNode-&gt;m_pParent = n2;
	}
}

template &lt; typename Key, typename Data &gt;
void BinaryTree&lt; Key, Data &gt;::DeleteNode( _TreeNode * pNode )
{
	assert( NULL != pNode &&
		"pNode cannot be NULL" );

	_TreeNode * n1 = NULL;
	_TreeNode * n2 = NULL;

	//! n1是要删除的节点
	if ( NULL == pNode-&gt;m_pLChild || NULL == pNode-&gt;m_pRChild )
	{
		//! 没有或者只有1个子节点，删除的节点时pNode
		n1 = pNode;
	}
	else
	{
		//! 左右子节点都有，先删除pNode的后继结点
		n1 = Successor( pNode ); // 该后继一定没有左节点
	}

	//! n2是n1唯一的一个子节点
	if ( NULL != n1-&gt;m_pLChild )
	{
		n2 = n1-&gt;m_pLChild;
	}
	else
	{
		n2 = n1-&gt;m_pRChild;
	}
	
	//! 为删除n1所作的指针修改
	if ( NULL != n2 )
	{
		n2-&gt;m_pParent = n1-&gt;m_pParent;
	}
	if ( NULL == n1-&gt;m_pParent )
	{
		// Root是唯一的一个父节点为空的节点
		m_pRoot = n2;
	}
	else
	{
		if ( n1 == n1-&gt;m_pParent-&gt;m_pLChild )
		{
			n1-&gt;m_pParent-&gt;m_pLChild = n2;
		}
		else
		{
			n1-&gt;m_pParent-&gt;m_pRChild = n2;
		}
	}

	if ( n1 != pNode )
	{
		//! pNode左右子节点都有的情况，
		//! 用pNode的后继结点的关键字和附加数据替换掉pNode的关键字和附加数据
		pNode-&gt;m_key = n1-&gt;m_key;
		pNode-&gt;m_data = n1-&gt;m_data;
	}

	delete n1;
}

template &lt; typename Key, typename Data &gt;
TreeNode&lt; Key, Data &gt; * BinaryTree&lt; Key, Data &gt;::Search( const Key & destK ) const
{
	_TreeNode *n = m_pRoot;
	while ( NULL != n && destK != n-&gt;m_key )
	{
		if ( destK &lt; n-&gt;m_key )
		{
			n = n-&gt;m_pLChild;
		}
		else
		{
			n = n-&gt;m_pRChild;
		}
	}

	return n;
}

template &lt; typename Key, typename Data &gt;
TreeNode&lt; Key, Data &gt; * BinaryTree&lt; Key, Data &gt;::Minimum( _TreeNode * pNode ) const
{
	assert( NULL != pNode &&
		"pNode cannot be NULL" );

	while ( NULL != pNode-&gt;m_pLChild )
	{
		pNode = pNode-&gt;m_pLChild;
	}

	return pNode;
}

template &lt; typename Key, typename Data &gt;
TreeNode&lt; Key, Data &gt; * BinaryTree&lt; Key, Data &gt;::Maximum( _TreeNode * pNode ) const
{
	assert( NULL != pNode &&
		"pNode cannot be NULL" );

	while ( NULL != pNode-&gt;m_pRChild )
	{
		pNode = pNode-&gt;m_pRChild;
	}

	return pNode;
}

template &lt; typename Key, typename Data &gt;
TreeNode&lt; Key, Data &gt; * BinaryTree&lt; Key, Data &gt;::Successor( _TreeNode * pNode ) const
{
	assert( NULL != pNode &&
		"pNode cannot be NULL" );

	//! 2 cases
	if ( NULL == pNode-&gt;m_pRChild )
	{
		//! 1: 右子树为空
		//! 向上查找直到遇到某个是其父节点的左儿子的节点时为止
		while ( NULL != pNode-&gt;m_pParent && pNode-&gt;m_pParent-&gt;m_pLChild != pNode )
		{
			pNode = pNode-&gt;m_pParent;
		}
		return pNode-&gt;m_pParent;
	}
	else
	{
		//! 2: 右子树不为空，后继即为右子树中的最左节点
		return Minimum( pNode-&gt;m_pRChild );
	}
}

template &lt; typename Key, typename Data &gt;
TreeNode&lt; Key, Data &gt; * BinaryTree&lt; Key, Data &gt;::Predecessor( _TreeNode * pNode ) const
{
	assert( NULL != pNode &&
		"pNode cannot be NULL" );

	//! 2 cases
	if ( NULL == pNode-&gt;m_pRChild )
	{
		//! 1: 左子树为空
		//! 向上查找直到遇到某个是其父节点的右儿子的节点时为止
		while ( NULL != pNode-&gt;m_pParent && pNode-&gt;m_pParent-&gt;m_pRChild != pNode )
		{
			pNode = pNode-&gt;m_pParent;
		}
		return pNode-&gt;m_pParent;
	}
	else
	{
		//! 2: 左子树不为空，前任即为左子树中的最右节点
		return Maximum( pNode-&gt;m_pLChild );
	}
}

#endif
</pre>

测试BST

<pre class="brush: cpp; title: ; notranslate" title="">#include "BinaryTree.h"

int num[11] = { 15, 6, 18, 3, 2, 4, 7, 13, 9, 17, 20 };

int _tmain(int argc, _TCHAR* argv[])
{
	BinaryTree&lt; int, int &gt; myTree;
	typedef TreeNode&lt; int, int &gt; Node;
	
	for ( int i = 0; i &lt; 11; i ++ )
	{
		int x = num[i];
		Node n( x, 0 );

		myTree.AddNode( n );
	}

	Node *pRoot = myTree.GetRootNode();
	Node *min = myTree.Minimum( pRoot );
	Node *max = myTree.Maximum( pRoot );

	Node *someNode = myTree.Search( 17 );
	Node *pre = myTree.Predecessor( someNode );
	Node *suc = myTree.Successor( someNode );

	myTree.DeleteNode( suc );

	return 0;
}
</pre>

_参考资料：算法导论P151-P158_
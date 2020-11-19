---
title: Replicate Monzo card animation
topimage: /assets/images/posts/monzo-cover.jpg
date: 01-11-2020
---

Monzo is an online bank based in the United Kingdom. It is very first app that I downloaded since I arrived here because it is easy to register and manage your account. Its design is so beautiful as well and in this article, I want to replicate the card animation of it, one of many things made a good impression on me.

## Monzo's card animation
It is a very nice animation of main credit card (red one) and pots which are card views. When scrolling, the main card will move a little up and left, then keep staying at center. It only moves in scroll flow when reaching the last card. Another point we also need to take into account is cards automatically stop at center when scrolling, it is very common feature that we often see in this sort of design.

![monzo-card-animation][monzo-card-animation]

## Custom collection view layout
The series of cards is actually a horizontal scroll direction collection view. And we also need a custom `UICollectionViewFlowLayout` to archive these animation. In this article, I assume you are familiar with `UICollectionView` and I will skip the code that create and configure collection view to focus on the custom collection view flow layout. You can find full code at [Github-MonzoCardAnimation](https://github.com/nhatquangz/monzo-card-animation)

Firstly we create `MonzoCollectionViewLayout` as a subclass of `UICollectionViewFlowLayout` and set this layout to our collection view.

```swift
...
let layout = MonzoCollectionViewLayout()
layout.minimumLineSpacing = 25
layout.scrollDirection = .horizontal
collectionView.setCollectionViewLayout(layout, animated: false)

class MonzoCollectionViewLayout: UICollectionViewFlowLayout {
///
}
```

I have created basic collection view having 10 cards with different colors. As yet we do nothing with the custom layout, this is how it looks like:
![initial-card][initial-card]

Next step, we will override method [`layoutAttributesForElements`](https://developer.apple.com/documentation/uikit/uicollectionviewlayout/1617769-layoutattributesforelements) to implement some changes.

```swift
...
class MonzoCollectionViewLayout: UICollectionViewFlowLayout {
	// 1
	private var allAttributes: [Int: UICollectionViewLayoutAttributes] = [:]
	
	// 2
	override func shouldInvalidateLayout(forBoundsChange newBounds: CGRect) -> Bool {
		return true
	}
	
	// 3
	override func layoutAttributesForElements(in rect: CGRect) -> [UICollectionViewLayoutAttributes]? {
		guard let attributes = super.layoutAttributesForElements(in: rect) else {
			return nil
		}
		// 4
		for att in attributes {
			let a = att.copy() as! UICollectionViewLayoutAttributes
			a.zIndex = att.indexPath.row == 0 ? 0 : 1
			allAttributes[att.indexPath.row] = a
		}
		// 5
		stickMainCard()
		
		// 6
		return allAttributes.values.filter { rect.intersects($0.frame) }
	}
```

1. We create a variable to save all collection view attributes. 
2. `shouldInvalidateLayout` function return `true` to update the layout changes of main card (the first one).
3. `layoutAttributesForElements` function is where you return the layout attributes for all items inside the given rectangle. Many complicated collection view layout require us to calculate manually size and position attributes, but in this case we do not have to do that. We just take all attributes that have already been calculated by the parent class `super.layoutAttributesForElements(in: rect)` and do a little changes for the attribute of first item (main card).
4. We can not change these attributes directly, so we create a copy save to `allAttributes` variable. We set `zIndex` of first item to 0 to make sure that it will stay under the other cards.
5. `stickMainCard` function is used to update the position of first item according to changes of scroll view.
6. Return any attributes with frames that intersect with the `rect`, in other words we return all visiable attributes.

`stickMainCard` function will be in charge of update the main card position according to scroll flow.

```swift
func stickMainCard() {
	if let att = allAttributes[0], let collectionView = collectionView {
		/// 1
		let itemWidth: CGFloat = UIScreen.main.bounds.width * 0.65
		let inset = collectionView.contentInset.left
		let contenOffsetX = collectionView.contentOffset.x + inset

		let dY: CGFloat = -25 // move up max distance
		let dX: CGFloat = -10 // move left max distance
		
		/// 2
		/// newX, newY: new item position when scrolling
		var newY = contenOffsetX / itemWidth * dY
		if newY < dY { newY = dY }

		var newX = contenOffsetX / itemWidth * dX
		if newX < dX { newX = dX }
		
		/// 3
		/// Dont change originY when collectionview is bounding (contenoffset < 0)
		att.frame.origin.y = contenOffsetX > 0 ? newY : 0

		let numberOfItem = collectionView.numberOfItems(inSection: 0)
		
		/// 4
		/// Set originX
		if let max = allAttributes[numberOfItem - 2]?.frame.origin.x {
			let x = min(contenOffsetX + newX, max - 10)
			att.frame.origin.x = contenOffsetX > 0 ? x : 0
		} else {
			att.frame.origin.x = contenOffsetX > 0 ? contenOffsetX + newX : 0
		}
	}
}
```

1. Some variable which is used to calculate the position of item. dX, dY have negative value as we want move the item in the opposite its axis.
2. `contentOffsetX` tell us how much distance we have scrolled and we will update the position according to this value. The animation is: the item changes its position when `contentOffsetX` changes and it reaches its threshold (dX, dY) when `contentOffsetX >= itemWidth`. 
3. Set item Y axis to the calculated value. Condition `contenOffsetX > 0` make sure that content is being scrolled from right to left (collectionview is not in bounding state).
4. Set item X axis. We can set directly item Y to calculated value as it does not change while scrolling, however for X axis we set `newX`value plus `contentOffetX` to keep the item in screen. When scrolling, you feel it does not change, but it is acctully moving along with scrollview's content.<br>
When reaching the last item, we want our card move along with the item before the last one. So we get `max` value which is the position of that item and update the card this value when `contentOffsetX + newX` exceed `max - 10`.(10 means that we want the left edge of our card stick a little before)

The last step we need to do is to make cards automatically scroll to center. We will override `targetContentOffset` function in the custom layout class `MonzoCollectionViewLayout` and return the offset that we expect. In this case, we will take the nearest card to center. To know which is the nearest card, we collect all visiable attributes and make a comparison to get the smallest value.

```swift
override func targetContentOffset(forProposedContentOffset proposedContentOffset: CGPoint, withScrollingVelocity velocity: CGPoint) -> CGPoint {
	guard let collectionView = collectionView else {
		return super.targetContentOffset(forProposedContentOffset: proposedContentOffset, withScrollingVelocity: velocity)
	}
	var offsetAdjusment = CGFloat.greatestFiniteMagnitude
	let horizontalCenter = proposedContentOffset.x + (collectionView.bounds.width / 2)

	let targetRect = CGRect(x: proposedContentOffset.x, y: 0, width: collectionView.bounds.size.width, height: collectionView.bounds.size.height)
	let layoutAttributesArray = super.layoutAttributesForElements(in: targetRect)

	layoutAttributesArray?.forEach({ (layoutAttributes) in
		let itemHorizontalCenter = layoutAttributes.center.x
		if abs(itemHorizontalCenter - horizontalCenter) < abs(offsetAdjusment) {
			offsetAdjusment = itemHorizontalCenter - horizontalCenter
		}
	})

	return CGPoint(x: proposedContentOffset.x + offsetAdjusment, y: proposedContentOffset.y)
}
```

### The final result:
![relication-result][relication-result]

**Github repository**: <https://github.com/nhatquangz/monzo-card-animation>
{: .notice--success}

[initial-card]: /assets/images/posts/initial-cards.gif
[monzo-card-animation]: /assets/images/posts/monzo-card.gif
[relication-result]: /assets/images/posts/final-result.gif
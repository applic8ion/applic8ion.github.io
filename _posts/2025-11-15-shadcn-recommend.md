---
layout: post
title: "[Shadcn] Shadcn 추천 UI"
date: 2025-11-15 21:21 +0900
categories: [WebDev, Shadcn]
tags: [WebDev, Shadcn]
image:
  path:
---

`Shadcn` 추천 UI를 포스팅한다.

## [Badge](https://ui.shadcn.com/docs/components/badge)

배지 또는 배지처럼 보이는 구성요소를 표시한다.

```bash
npx shadcn@latest add badge
```

## [Card](https://ui.shadcn.com/docs/components/card)

머리글, 내용 및 바닥글이 있는 카드를 표시한다.

### Install

```bash
npx shadcn@latest add card
```

### Example

```react
<Card className="w-full flex flex-row items-center justify-between bg-transparent hover:bg-card/50">
  <CardHeader>
    <CardTitle className="text-2xl font-semibold leading-none tracking-tight whitespace-nowrap">
      {name}
    </CardTitle>
    <CardDescription className="text-muted-foreground whitespace-nowrap">
      {description}
    </CardDescription>
    <div className="flex items-center gap-4 mt-2">
      <div className="flex items-center gap-px text-xs text-muted-foreground">
        <MessageCircleIcon className="w-4 h-4" />
        <span>{commentsCount}</span>
      </div>
      <div className="flex items-center gap-px text-xs text-muted-foreground">
        <EyeIcon className="w-4 h-4" />
        <span>{viewsCount}</span>
      </div>
    </div>
  </CardHeader>
  <CardFooter className="py-0">
    <Button variant="outline" className="flex flex-col h-14">
      <ChevronUpIcon className="size-4 shrink-0" />
      <span>{votesCount}</span>
    </Button>
  </CardFooter>
</Card>
```

## [Typography](https://ui.shadcn.com/docs/components/typography)

`h1`, `h2`, `h3`, `p` 등 태그에 텍스트 사이즈 등을 수동으로 지정하지 않고, 일관된 스타일로 적용되게 해준다. 별도 설치할 필요 없다.

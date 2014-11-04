---
date: "2012-05-01 18:57:22+0800"
layout: post
title: "WP应用增加邮件反馈和评分"
categories: 技术
tags: WP7 邮件反馈 feedback
---


    private void FeedBackBtn_Click(object sender, RoutedEventArgs e)
    {
            try
            {
                EmailComposeTask task = new EmailComposeTask();
                task.To = "zhao.qinlong@gmail.com";
                task.Subject = "口袋秘书软件意见反馈";
                task.Body = "";
                task.Show();
            }
            catch (Exception)
            {
            }    
    }

    private void button1_Click(object sender, RoutedEventArgs e)
    {
		try
			{
				new MarketplaceReviewTask().Show();
			}
		catch (Exception)
		{
		}

    }


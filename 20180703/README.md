# ARTS-20180703

## Algorithm

[Linked List Cycle II - LeetCode](https://leetcode.com/problems/linked-list-cycle-ii/description/)

The fast-slow pointers are well known O(1) space to check whether there is a cycle in a linked list. However, in this problem, the difficulty is that how we can find the beginning of the cycle.

Suppose the beggining of the cycle is node `C` and it takes `x` steps from head to `C`. And fast and slow pointers meets at node `M` in the cycle. It takes `y` steps from `C` to `M` and `z` steps from `M` to C. 

```
head->(x)->C->(y)->M->(z)->C
```
The length of path where fast pointer travelled is `x+y+z+y`. On the other hand, the slow pointer took `x+y` steps and met the fast pointer at `M`. As the fast pointer is twice faster than the slow pointer, `x+y+z+y` is twice longer than `x+y`. Hence, `x=z`.

In other words, after two pointers met at `M`, we can let two pointers start from `head` and `M` with same speed until they meet again. The second point they meet will be the node `C`, the beginning of the cycle.

```python
# Definition for singly-linked list.
# class ListNode(object):
#     def __init__(self, x):
#         self.val = x
#         self.next = None

class Solution(object):
    def detectCycle(self, head):
        """
        :type head: ListNode
        :rtype: ListNode
        """
        fast = slow = head
        while fast != None:
            slow = slow.next
            fast = fast.next
            if fast == None:
                return None
            fast = fast.next
            if slow == fast:
                fast = head
                while fast != slow:
                    fast = fast.next
                    slow = slow.next
                return slow
        return None
```

## Review

[Mentorship is more important than ever – Kevin Crabbe – Medium](https://medium.com/@kevincrabbe/mentorship-is-more-important-than-ever-732ccb413cac)

Mentorship also helped me a lot when I just graduated and started the career. When my mentor left the company, I truly found out the mentorship is indeed useful in so many aspects.

The real-world problems are quite different from problems in school. They are too ambiguous and sometimes you cannot find the key point of the problem. Moreover, there could be a lot of solutions to a problem though some of them may not fit in your case because of something you have known yet. However, a mentor could help you a lot to learn how to find the key point of the problem by giving hints and review possible solutions to avoid detours.

Furthermore, a mentor could be someone you can follow up questions and learn more. It is a wonderful thing that you have someone you can argue with, learn from and rely on. Much better than StackOverflow and Google, isn't it? :P

## Tips
Terraform loop (count) in resource fields (nested block loop).

Workaround for [Support count in resource fields · Issue #7034 · hashicorp/terraform · GitHub](https://github.com/hashicorp/terraform/issues/7034).
```
data "null_data_source" "foo" {
  count  = 2
  inputs = {
    group = "${element(google_compute_instance_group.bar.*.self_link, count.index)}"
  }
}
resource "google_compute_region_backend_service" "baz" {
  ...
  backend = ["${data.null_data_source.foo.*.outputs}"]
}
```

## Share

Containerization is not a panacea for all systems.

Container techniques are obviously a trend. Many companies start using containers to solve their problems in recent years. However, IMHO, in some cases, containerization does not bring so many benefits as you expect, especially with IaaS.

The benefits may be expected:
1. Fast horizontal scaling
2. High resource density and low costs
3. Consistent environment
4. Write once run everywhere
5. Isolation

For fast horizontal scaling, it is true when you have enough host resources and a container can be started very quick from an image. However, on the cloud, if you always have so many resources, `a)` why not provision those resources for the application in advance? `b)` would it better to scale the resources for the load? If the load pattern is simple and clear, and the resources are auto-scaling  with the load, there may not be enough host resources for a new container. Provision a new host for containers will not be faster than provisioning a new host for application on the cloud (IaaS).

Furthermore, considering stateful applications like storage, database services. The difficult and time-consuming part is about data itself. I do not think containers will help a lot in this case.

High resource density and low costs are similar to scaling problems. Considering the question `b)` mentioned before, it actually talks about costs. High density and low costs can be achieved by well-organized applications and types of instances.

When it comes to the consistent environment, it is ensured by images and same provisioning processes. In fact, images are also available in most cloud services and also possible for bare-metal hardware. Provisioning processes are also often ensured by tools like `Chef`, `Ansible`, etc.

Finally, "write once run everywhere" is not always a requirement. Many back-end applications do not need to run cross platforms. It is also true for isolation. Isolation can be achieved by many other techniques such as IP alias, Linux namespaces (parts of them), and file systems. For example, the file system can be simply achieved by `unshare --mount`, and `overlayfs` can help share storages cross namespaces.

Sometimes, I think that, compared to containerization itself, people benefit more from orchestration solutions if they do not have a good one.
